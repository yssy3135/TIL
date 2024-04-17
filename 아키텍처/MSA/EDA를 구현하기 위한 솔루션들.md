### EDA의 도입이 합리적일 수 있는 경우

잘못된 한번의 Event 처리가 굉장히 큰 여파르 ㄹ불러올 수 있다.

- 빈번하게 서버나, DB엣 Sync 요청에 대한 실패가 잦은 경우
- Message Broker를 잘 이해하고 관리할 수있는 별도의 조직이 있는 경우
- 각 도메인데이터의 변경 History가 굉장히 중요한 비즈니스인 경우
- Event의 처리 혹인 Message Broker가 실패하더라도, 완벽한 fallback 정책이 구현된 경우

### Eventuate

**Eventuate Local**

- Event 를 기반으로, 하나의 서비스(Local) 환경에서 Event Sourcing 플랫폼 구현을 도와주는 오픈소스에요.
- Event Driven Domain

**Eventuate Tram**

- 분산 데이터 환경을 도와주는 플랫폼 오픈소스로, Saga, CQRS 패턴 구현을 쉽게 만들어 주는 오픈소스에요.
- Event Driven Platform for Distributed data management

EDA를 위한 오픈소스는 아님

### Axon Framework

- 태생부터 DDD, CQRS, EDA 구현을 쉽게 하기 위해 개발된 오픈소스 프레임 워크

Axon Framework

- 별도의 오케스트레이터(조율자)가 존재
- Event Sourcing 기반으로, 데이터를 관리할 수 있는 도구를 제공하고 이 도구들을 사용해서 CQRS, DDD를 구현할 수 있도록 도와준다.
- Springboot 프로젝트에 의존성으로 사용하여, 다양한 어노테이션 기반으로 위 기능을 구현할 수 있다.

Axon Server

- Axon Framework를 사용하여 배포된 어플리케이션들의 조율자 역할을 한다.
- Axon Framework를 사용하는 어플리케이션들로부터 발행된 Event들을 저장하는 역할을 한다.
- 각 어플리케이션들의 상태를 관리하고, imbedding된 큐일을 내재하여 유량 조절도 진행
- 고가용성에 집중된 내부 구현이 되어있고, Observability를 제공

**Axon Framework 를 구성하는 요소**
**Command Bus**

- Command 만이 지나갈 수 있는 통로

**Command Handler**

- Command Bus 로부터 받은 Command를
  처리할 수 있는 handler

**Domain, Aggregate**

- based on DDD
- Aggregate: Command 를 할 수 있는 도메인
  의 단위

**Event Bus**

- Event 만이 지나갈 수 있는 통로

**Event Handler**

- Event Bus 로부터 받은 Event 를 처리하는 Handler


![EDA를 구현하기 위한 솔루션들_1.png](images%2FEDA%EB%A5%BC%20%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0%20%EC%9C%84%ED%95%9C%20%EC%86%94%EB%A3%A8%EC%85%98%EB%93%A4_1.png)