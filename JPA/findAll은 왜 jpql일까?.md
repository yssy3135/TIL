# findAll은 왜 jpql일까?

findAll에서는 N+1이 발생한다 왜 그런것 일까?

기본적으로 제공하는 메서드이지만 jpql인 것일까? 왜그럴까?

### JPQL 이란?

SQL이 데이터베이스 테이블을 대상으로 하는 데이터 중심의 쿼리라면 JPQL은 엔티티 객체를 대상으로 하는 객체지향 쿼리다.

JPQL(Java Persistence Query Launguage)는 엔티티 객체를 조회하는 객체지향 쿼리다.

JPQL은 SQL을 추상화해서 특정 DB에 의존하지 않는다.

**findAll 구현을 따라가보면 getQuery 메서드를 사용하고 getQuery메서드는 Criteria를 사용한다.**

![1.png](img%2FfindAll%EC%9D%80%20%EC%99%9C%20jpql%EC%9D%BC%EA%B9%8C%3F%20image%2F1.png)

![2.png](img%2FfindAll%EC%9D%80%20%EC%99%9C%20jpql%EC%9D%BC%EA%B9%8C%3F%20image%2F2.png)

![3.png](img%2FfindAll%EC%9D%80%20%EC%99%9C%20jpql%EC%9D%BC%EA%B9%8C%3F%20image%2F3.png)

**Criteria는 JPQL을 생성하는 빌더 클래스이다.**

그렇기때문에 `CriteriaBuilder` 를 사용해서 쿼리를 생성해준다.

### JPQL은 왜 N+1 문제를 발생시킬까?

JPQL은 SQL을 추상화해서 특정 DB에 의존하지 않는다고 했었다. 그렇기 때문에 특정 SQL에도 종속되지 않고 엔티티 객체와 필드 이름을 가지고 쿼리를 한다.

JPQL입장에서는 연관관계 데이터를 무시하고 해당 엔티티 기준으로 쿼리를 조회하기 때문이다.

그 이후 연관관계에 따라 연관된 엔티티 데이터가 필요한경우 별도로 호출하게 된다

### JPQL은 fetchType.Eager를 설정해도 N+1문제를 발생시키는 것도 이때문이다.

findAll을 하면 해당 테이블에 있는 모든것을 가져와야 한다.

그렇기 때문에 update를 해야한다. 현재 트랜잭션이 아닌 다른 트랜잭션에서도 작업이 있어 테이블에 어떤 변화가 있었을지 모르기 때문이다.

현재 트랜잭션 안에서도 모두 반영하고 select를 해야 가장 최신 상태의 데이터를 가져오는 것이기 때문이다.