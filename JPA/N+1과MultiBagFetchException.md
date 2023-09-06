# N+1 과 MultiBagFetchException

## 먼저 N+1 문제란 무었일까?

조회된 엔티티가 가지고 있는 ToMany, ToOne 으로 매핑된 자식 엔티티들의 쿼리가 추가로 수행되는 현상이다.

### N+1 문제가 발생하는 케이스들을 가정해보자

1. JPQL을 사용하여 연관된 엔티티를 조회할 경우
2. FetchType.Lazy 전략을 사용할 경우
3. FetchType.eager + jpql을 사용할 경우

### 왜 N+1 문제가 발생할까?

**JPQL을 사용 할 경우**

- JPQL(Java persistence Query Laguage)는 작성된 JPQL을 그대로 SQL로 번역한다.

  때문에 Fetch Type 옵션의 영향을 받지 않는다.

- 또한 JPQL은 1차 캐시와 무관하게 항상 SQL로 변경되어서 실행된다.

**Lazy 전략을 사용할 경우**

- 연관된 엔티티가 실제로 필요할 때까지 로드를 지연시켜주어 성능 최적화에 도움을 준다.
- 하지만 필요할 때마다 추가적인 DB 접근이 필요해진다.

**FetchType.eager + jpql을 사용할 경우**

jpql에서 fetch join을 하지 않고 FetchType을 eager로 지정했다면

jpql을 sql로 변환하고 쿼리가 날아간다.

그 이후 fetchType 옵션을 확인하여 N번의 쿼리가 추가로 실행되게 된다.

공식문서에서도 이 방법은 권장하지 않는다.

https://docs.jboss.org/hibernate/orm/5.5/userguide/html_single/Hibernate_User_Guide.html#fetching-strategies-dynamic-fetching-entity-graph

![image](https://github.com/yssy3135/TIL/assets/62733005/e21365b3-efd6-4ad7-ba92-227f258d4a48)

번역하자면 JPQL 쿼리에서 EAGER 연관관계가 생략된 경우(fetch join 생략)  Hibernate는  각 객체의 EAGER연관관계를 조회하기 위해 또 다른 조회문을 실행하여 N+1문제가 발생한다.

LAZY로 설정하고 필요할때 쿼리별로 EAGER를 적용해라.

**************************그럼 어떻게 N+1 문제를 해결할까?**************************

N+1 문제를 해결하기 위해 여러 방법이 있다.

**eager를 사용하지 말고 lazy로 설정한뒤 즉시로딩이 필요한 부분에 fetch join을 사용하자!**

하지만 이 방법에도 문제가 있다.

pagination을 사용할 경우 in memory를 적용해서 조인하여 반환해준다.

이 뜻은 모든값을 select해서 인메모리에 저장하고 필요한만큼만 반환해주는 것이다.

이렇게 되면 pagination의 의미가 사라지고 OOM(Out Of Memory가 발생할 확률이 매우 높아진다)

**그럼 pagination을 사용하는 부분에는 어떻게 해결하지?**

조회하려는 필드에 @BatchSize 어노테이션을 설정하거나

global batch size 옵션을 활용한다.

그렇게되면 한번에 로딩할 엔티티의 개수를 조절할 수있어 SQL 쿼리 실행 횟수를 줄여 성능을 개선 할 수 있다.

## 여러개의 toMany로 매핑된 엔티티를 fetch join 할때 MultiBagException를 고려해야 한다!

**언제 발생할까?**

말 그대로 여러개의 toMany로 매핑된 엔티티를 fetch join 하려고 할 때 발생한다.

**왜 발생할까?**

- Hibernate에서는 Bag이라는 컬렉션을 사용한다.
- **이 Bag은 Set처럼 순서가 없고 List 처럼 중복이 허용된다**
- **Java Collection에서는 Bag이 존재하지 않아 List를 Bag 처럼 사용한다.**
- 이 때, 엔티티에서 두개의 Bag을 가져오게 되면 데카르트 발생할 수 있다.
- Bag에는 순서가 없기 때문에 Hibernate에서는 중복이 발생할수 있고 올바르게 엔티티에 매핑 할 수 없는 것.

### 그럼 어떻게 해결해야 할까?

일단 List 형식의 두개의 ToMany로 매핑된 자식 엔티티는 fetch 할 수 없다 그러므로

- 순서나 중복이 필요 없는경우 Set으로 변경 한다.
  - Set은 하이버네이트에서 SetType으로 취급하기 때문에 Bag 유형이 하나만 존재하는 경우에는 동시에 컬렉션을 가져올 수 있다.
- 가장 많은 정보를 가진 엔티티를 fetch join으로 조회하고 나머지를 batch size설정을 통해 in절로 조회되도록 구성한다.