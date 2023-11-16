
### JPQL vs Querydsl

- 컴파일 시점에 오류를 잡을 수 있다.
    - Q타입을 만들어서 자바 코드로 풀어낼 수 있다.
    - 파라미터 바인딩을 자동으로 해결해줌.
    - 쿼리도 자바 코드로 작성


**JPAQueryFactory는 필드에서 선언하고 사용해도 무방하다**
- JPAQueryFactory를 생성할때 제공하는 Entitymanager(em) 에 달려있음
- 스프링에서는 여러 쓰레드가 동시에 EntityManager에 접근해도 트랜잭션에 따라 별도의 영속성 컨텍스트를 제공하기 때문에 무방하다.



### 기본 Q-Type 활용

기본 인스턴스를 static import와 함께 사용하는 것을 권장한다.

Querydsl은 JPQL 쿼리 빌더로 JPQL로 변환되어서 나감.
JPQL 쿼리를 보고싶을 경우 아래 설정을 추가하면 확인 가능하다

```
spring.jpa.properties.hibernate.use_sql_comments: true
```


**세타조인**
- 연관관계가 없는 필드를 조인
- from 절에 여러 엔티티 선택
- 외부 조인이 불가능 -> 조인 on 을 사용하면 외부 조인이 가능


on 절을 활용해 조인 대상을 필터링 할때 inner 조인을 사용한다면 where 절에서 필터링 하는 것 과 동일
더 익숙한 where 절을 사용하자.
꼭 필요한 경우에만 사용!


서브쿼리에서 from 절을 지원하지 않음.
JPQL 에서 지원하지 않기 때문에 JPQL 쿼리 빌더인 Querydsl에서도 지원하지 않는다.

from 절 서브쿼리 해결 방안
1. 서브쿼리 join으로 변경 ( 가능한 상황이 있고, 불가능한 상황이 있음.)
2. 애플리케이션에서 쿼리를 2번 분리해서 실행
3. nativeSQL 사용

**from 절의 서브쿼리는 생각의 전환이 필요하다.**
한번에 긴 쿼리를 날리는 것 보다
나누어서 여러번 날리는 것이 효과적일 수 있다.


**Tuple 조회에서 Tuple 타입은 Repository 계층에서만 사용하자**
- service나 controller 넘어가는 것은 좋은 설계가 아님
- 상위계층(controller, service) 계층이 하부 구현기술에 종속(의존)되기 때문에 구현기술이 변경되면 모든 계층이 영향을 받게 된다.
- 변환하여 DTO에 담아서 사용하자



**Querydsl 에서 projection**
프로퍼티 접근 - Setter
- 기본 생성자 만들어주지 않음.

프로퍼티 접근 - Field
- getter, setter 없어도됨.

프로퍼티 접근 - constructor
- 생성자 타입 순서와 맞아야함.


**QueryProjection**
생성자 타입과 다른점
- 생성자 타입은 런타임에 오류가 터짐
- 컴파일 시점에 잡지못함

장점
- 컴파일 시점에 타입 검사 가능

단점
DTO 는 Querydsl을 몰랐는데 Querydsl과 의존성이 생김
- DTO 까지 Q 파일로 만들어야 한다.


**동적쿼리**
BooleanBuilder
- 초기값 설정 가능
- builder 끼리 조합 가능
  Where
- 메서드를 가른 쿼리에서도 재활용 가능
- 쿼리 자체 가독성이 높아짐


**bulk연산**
- 영속성 컨텍스트와 DB의 상태가 달라진다.
    - 영속성 컨텍스트를 무시하고 DB에 반영됨.
    - DB에서 가져와도 영속성 컨텍스트에 값이 존재하면 DB에서 가져온 값을 버림
        - repeatable read 발생
    - 영속성 컨텍스트가 항상 우선권을 가짐'


**SQL function 호출**
- 사용하는 방언에 등록이 되어있어야함.



**countQuery최적화**
- count쿼리가 생략가능한 경우 생략해서 처리
- 페이지 시작이면서 컨텍츠 사이즈가 페이지 사이즈보다 작을때
- 마지막 페이지 일때 (offset + 컨텐츠 사이즈 더해서 전체 사이즈 구하기)
    - 마지막 페이지 이면서 컨텐츠 사이즈가 페이지 사이즈보다 작을 때

