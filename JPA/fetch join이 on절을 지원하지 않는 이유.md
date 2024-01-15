### JPA 표준 스펙에는 fetch join 대상에 별칭이 없다. 하지만 하이버네이트는 허용한다.

→ fetch join 대상에 별칭을 사용은 할 수 있지만 문제가 발생할 수 있어 주의해서 사용해야 한다.

hibernate 참조

> HQL(hibernate Query Language) 에서  Fetch Join에 별칭(Alias)가 필요한 유일한 이유는 추가 컬렉션(N방향) 을 재귀적으로 조인 하여 가져오는 경우밖에 없다.
>

## Fetch Join의 한계

### Fetch Join의 대상은 별칭을 줄 수 없다.

- select, where 서브쿼리 등 에서 fetch join대상을 사용할 수 없다.
- 하이버네이트 자체에서 별칭을 지원 하지만 연관된 데이터 수에 대한 무결성이 깨질 수도 있다.
- 2개 이상의 xToMany 관계에서는 모든 연관 엔티티에 Fetch Join을 적용할 수 없다.
    - default_batch_fetch_size 를 사용하여 in 절로 한번에 호출하도록 할 수 있다.
    - List가 아닌 set 을 사용하면 가능하다. (Hibernate 내부 자료구조를 Bag이라는 타입을 사용하기 때문이다)

## Fetch Join에서 Where절로 사용하게 된다면?

Fetch join에서 on 절은 지원하지 않지만 where 절은 사용할 수 있다.

하지만 where절을 잘못사용할 경우 문제가 일어날 수 있기 때문에 조심해야 한다.

******하이버네이트에서는 Alias를 지원하지만 데이터 무결성이 깨질 수 있다.******

Team 과 Member가 oneToMany로 매핑되어 있다고 가정해볼때

Team1 - Member1

Team1 - Member2

Team1 - Member3

이렇게 연관관계가 매핑되어 있다고 해보자

Fetch join에 ON 절을 사용할 경우 에러가 발생할 것이다.

```jsx
SELECT * FROM Team t JOIN fetch t.member m ON m.id != 1  WHERE t.id = 1
```

이렇게 조회를 한다고 해보자

```jsx
SELECT * FROM Team t JOIN fetch t.member m WHERE t.id = 1 and m.id != 1
```

결과가 어떻게 될까?

Team1 - Member2

Team1 - Member3

Team1에 매핑된 Member중 id 가 1일 Member를 제외하고 조회될 것이다

**예상된 결과이지만 여기서 문제가 일어날 수 있다!**

1. DB 와의 무결성이 깨진다 DB 상에는 Team1과 Member1,Member2,Member3이 매핑되어 있는 상태이지만 Member1이 제외되었기 때문에 DB와 데이터가 일치하지 않는 상황이 발생한다.