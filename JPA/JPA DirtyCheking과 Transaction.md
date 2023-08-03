# Dirty Cheking이란?

Transaction 안에서 엔티티의 변경이 일어날 경우 변경 내용을 자동을 데이터베이스에 반영하는 JPA 특징

### JPA 에는 update는 존재하지 않는다.

- entity save를 진행할 경우 isNew메서드를 통해 entity가 새로운 Entity인지 존재하는 Entity인지 판단한다.
- 존재할 경우 merge를 진행
- 새로운 Entity라면 persist를 진행한다.

```java
@Transactional
@Override
public <S extends T> S save(S entity) {

	Assert.notNull(entity, "Entity must not be null.");

	if (entityInformation.isNew(entity)) {
		em.persist(entity);
		return entity;
	} else {
		return em.merge(entity);
	}
}
```

### 어떻게 엔티티의 변경을 감지할까?

- JPA에서 영속성 컨텍스트가 관리하고 있는 엔티티를 조회하면 해당 엔티티의 조회 상태로 **스냅샷**을 만들어 놓는다.
- 트랜잭션이 끝나는 시점에 스냅샷과 비교해서 변화가 생긴다면 update해 DB에 반영한다.

### 스냅샷이란?

- 엔티티를 조회해 영속성 컨텍스트에 보관할 때 초기 상태를 복사해서 저장해놓는다.
- 플러시 시점에 스냅샷과 엔티티 상태를 비교해서 변경된 부분을 찾는다.
- update 쿼리문을 지연 SQL저장소에 저장해 두었다가 트랜잭션 커밋시 DB에 반영한다.

### DirtyChecking이 일어나는 환경

- 영속(managed) 상태
- Transaction 안에서 엔티티를 변경하는 경우

### Transactional과 DirtyChecking의 연관성

********@Transactional********

JPA를 이용 하여 데이터를 읽고 쓰고 저장하는 모든 데이터 연산에 트랜젝션 단위가 이용된다.

**트랜젝션 단위를 사용하는 이유**

1. 트랜젝션이 시작되었을때 다른시점에 시작된 트랜젝션과 서로 영향을 줄수 없어 격리성이 유지되며 에러 발생시 Rollback 하는 방법으로 일부만 남아있는것이 아닌 정상 **전체저장** 비정상 **전체롤백**을 통해 그 원자성을 유지함.
2. 영속성 컨텍스트의 엔티티 자원관리 기능을 효율적으로 사용하기 위함.

**코드를 보면서 알아보자**

1. **service 메소드 단위에 Transactional**

```java
		@Transactional
    public UserDTO findUserById(Long userId){
        Optional<User> userOptional = userRepository.findById(userId);

        if(userOptional.isEmpty()){
            throw new NotFoundException("not found user userId : " + userId);
        }

        User user = userOptional.get();

        user.updateEmail("update@user.com");
        
        return UserDTO.ofUser(user);
    }
```

이 경우에 user는 업데이트가 되었을까 안되었을까?

정답은 update가 되었다.

메소드 단위에 Transactional이 걸려있기 때문에 메소드가 호출될 경우 트랜잭션을 시작하고 커밋하게된다.

그렇게 때문에 save함수를 호출안했더라도 Dirty Checking에 변경이 감지되어 user Entity는 update된다.

1. **Transactional 사용하지 않음.**

```java

    public UserDTO findUserById(Long userId){
        Optional<User> userOptional = userRepository.findById(userId);

        if(userOptional.isEmpty()){
            throw new NotFoundException("not found user userId : " + userId);
        }

        User user = userOptional.get();

        user.updateEmail("update@user.com");
        
        return UserDTO.ofUser(user);
    }
```

이경우에는 업데이트가 되지 않는다.

DirtyChecking은 Transaction단위로 실행되는데 jpa 자체적으로 SimpleJpaRepository에 Transactional이 걸려있어

findById 되는 시점에만 트랜잭션이 시작되고 종료된다.

그렇기 때문에 entity가 변경되었어도 변경이 감지되지 못하기 때문에 DB에 반영되지 않는다.

1. **Transactional(ReadOnly = true)**

SimpleJpaRepository는 타입에 readOnly 트랜잭션이 적용되어 해당 어노테이션을 오버라이딩 하지 않은 모든 메서드는 readOnly트랜잭션을 사용한다.

따라서 readOnly 트랜잭션이면 성능 최적화를 위해 엔티티의 스냅샷(딥카피)를 만들지 않는 걸 알 수 있다.

이경우도 위와 마찬가지로 DB에 반영되지 않는다.