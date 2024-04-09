### Async패턴, Saga 패턴의 공통점

- 비동기 통신 방식 사용
- 메시징 블커를 사용
- Event를 발행(Produce) 하고 비동기 방식으로 필요한 곳에서 Consume 한다.

### Event의 특성은?

- 발행(Produce) 이라는 행동은, 비교적 성공률이 굉장히 높다. (누락될 가능성이 거의 없다.)
- 하나의 표지만 같은 역할로 Event 자체만으로는 그 어떤 도메인과도 직접적인 의존성이 없다.
- 느슨한 결합을 가능하게 만든다.

### 일반적 MSA 환경에서 Sync 방시그이 서버와 RDB를 사용하다 보면 생기는 문제

- DB가 불안정해 빈번하게 CRUD 요청이 실패하기도 한다.
- 서버의 리소스가 부족해서, 불안정해서, 빈번하게 요청 자체를 못받는 겨우도 생긴다.
- 서비스간 호출이 재시도 되면서 예기치 못한 문제가 발생하기도 한다.
- 실패한 요청들은 이후 access log, 혹은 gateway 레벨에서의 로그 등을 확인하고 데이터 의 정합성을 맞추는 작업이 추가적으로 필요하다.

### Event를 통해서 해결해 볼 수는 없을까?

- 모든 데이터의 변경을 의미하는 작업들에 대해서 최종적으로 변경된 정보(RDB)만 바라보는 것이 아니라
  마지막에 수신한 Event를 기준으로 특정 도메인 데이터의 정합성을 유지해 볼 수도 있지 않을까?

기존의 방식 → 데이터 영속화 방식

새로운 방식 → Event Driven 방식 (using Event Sourcing)

- 이벤트 를 기준으로 모든 데이터의 변경을 처리, 조

### 데이터 영속화 방식의 특징

- 하나의 도메인(DB)를 특정할 수 있는 Key(e.g Primary Key)를 조건으로, 포함된 정보를 변경
- 조회시 하나의 도메인(DB)를 특정할 수 있는 Key(e.g. Primary Key)를 조건으로, row정보를 모두 조회
    - 서버, DB에 문제 발생 시 특정 요청에 대해 rollback이 된 상태로, 재시도 등의 대처를 해야만 한다.


### Event Driven 방식의 특징

- **데이터 변경**
    - 하나의 도메인(DB)를 특정할 수 있는 Key(e.g. Primary Key)와 변경 정보가 담긴 이벤트를 발행(Produce) 한다
        - 동기 : 사용자 -> (( 주소 변경 요청 )) -> membership svc -> 변경 요청 Event 발행 -> ( 사용자 응답 완료 )
        - 비동기 : membership svc-> 변경 요청 Event 소비(Consume) -> 도메인의 최종 Version 정보를 변경.
- **데이터 조회**
    - 하나의 도메인(DB)를 특정할 수 있는 Key(e.g. Primary Key)를 조건으로, 최종 Version 정보를 조회

**장애나 서버, 인프라에 문제가 발생할지라도, 앱이 복구되면 메시지 브로커(이벤트)에 의존해서 일관성(트랜잭션)을 구현할 수 있다.**

### Event Driven 뭐가 좋은데

- 비동기 방식의 특성과 자원의 효율적 사용
    - Request Driven
        - 100 request → 100 processing
    - Event Driven
        - 100 request → 1 processing
        - 소비하는 서버에서 이벤트를 쌓아놓고 있다가 필요에 따라 한번에 처리할 수도 있다.
- 데이터 정합성

### Event Driven Architecture(EDA) - MSA의 단점을 보완

- 모든 데이터의 변경과 조회를 이벤트 기반(Event Driven)
- Event를 관리하는 계층이 필요
- 
![이벤트 소싱과EDA_1.png](images%2F%EC%9D%B4%EB%B2%A4%ED%8A%B8%20%EC%86%8C%EC%8B%B1%EA%B3%BCEDA_1.png)

### Event Driven Architecture(EDA)를 구성하는 요소

![이벤트 소싱과EDA_2.png](images%2F%EC%9D%B4%EB%B2%A4%ED%8A%B8%20%EC%86%8C%EC%8B%B1%EA%B3%BCEDA_2.png)

### Event Driven Architecture(EDA)의 치명적인 단점

- Event가 한번 잘못 발행, 혹은 Consume이후 잘못된 처리를 하는 순간 모든 데이터 정합성과 트랜잭션이 크게 망가질 수 있는 상황이 발생한다.
    - 백업, 통제 정책 등을 잘 수립해야만 한다.
    - risky한 비즈니스에서는 오히려 안 맞을 수 있다.
    - 몇가지 solution들이 존재한다.
        - Eventuate, Axon Framework
- Sync 방식을 유지하며, Infra와 DB Scale Up 등의 힘으로 유지하는 선택도 분명 하나의 선택지이다.