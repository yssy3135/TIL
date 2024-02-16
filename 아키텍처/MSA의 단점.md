### 개발/운영 측면

**서비스간 통신**

**모놀리식**

- 다른 서비스를 호출하기 위해 인터페이스, 클라이언트 메서드를 호출
    - Spring(boot) ↔ Middleware (JPA-ORM, Mybatis,…) ↔ DB

**MSA**

- 다른 서비스를 호출하기 위해 타 서비스 호출을 위한 통신(HTTP/gRPC)를 활용

  gRPC, HTTP 에 대한 기본적인 이해 필수 요소
  -> (e.g. Keepalive? Connection Pool? , Connection/Business Logic Timeout 차이? )

- 통신에 대한 기본적인 오류나 방식과 응답이 무었이고 응답하는 결과값에 대해서 판단할 수 있어야 한다.

### CI/CD 측면

- 지속적인 통합과 배포(CI/CD)환경에서, 현재 Production 상황은 어떤지? 나의 feature가 포함되어 있는지?
- Devops, SRE가 책임질 수 있는 부분들에는 한계가 존재
- Logging, Monitoring등 어느정도 비즈니스 로직 전반에 대해 각 서비스 간 동작 방식을 알아야만 트러블 슈팅 등이 가능하다.

### Transactions

**모놀리식**

- SpringBoot @Transaction
    - 다른 서비스를 호출하기 위해 인터페이스, 클라이언트 메서드를 호출 하기때문에 Transaction으로 쉽게 해결
- DB Resource

**MSA**

- Transaction은 하나의 서비스에서 유효하기 때문에 분산서비스에서는 해결이 불가능하다.
- 2PC (2 Phase Commit)
- 보상 트랜잭션 - Saga
    - 취소하기 위한 작업을 통신을 통해 알려주어야 한다.

**Data Query의 어려움**

- 모놀리식은 2~3번의 join으로 다른 서비스의 데이터를가져올 수 있지만
- msa는 서로 다른 서비스의 데이터를 통신을 통해 가져와서 조합해주어야 한다

### Monitoring

**모놀리식**

모니터링의 대상

- 인프라(서버, DB) 의 상태 (CPU, Memory, ...)
- 네트워크 Status (Inbound / Outbound Proxy Latency, ...)
- 어플리케이션 상태 (JVM Status, App Warning, Error, ...)
- 1개의 Process 1개의 JVM에

MSA

모니터링의 대상

- for Monolithics(인프라, 앱 상태, ...)
- 서비스 간 응답, 네트워크 상태 (Latency, Error rate, ...)
- 어떤 트랜잭션이 어느 구간에서 왜 문제가 발생했는지!?

**모니터링을 위한 metric수집**

보통 pull 방식을 사용

- metric을 수집하기 위해 각 서비스에 부담을 주는것은 아니다
- 주기적으로 수집하는 polling 방식을 사용
- push 방식을 사용할 때는 cron job 같은 방식은 push방식을 사용
    - 언제 작업이 수행되는지 정확히 파악하기 어렵다
    - 워낙 짧은 시간 올라와있다 내려가있을수 있는 확률이 크다.



결론은...

- MSA(분산 시스템) 은 난이도가 높다.
- 통신, 트랜잭션, 모니터링 등 굵직하고 해결하기 어려운 문제들이 많다.
- 그러나, AWS, Cloud 기반 기술(Docker, K8S, ...) 등의 등장으로 많은 부분들이 해결되었다.
- MSA 환경에서 모니터링을 위한 다양한 기술 스택이나 이론도 많이 나왔다. (Jaeger, Kiali, Zipkin - Circuit Breaker, ...)
- 그럼에도 전체적인 동작 방식이나 구조를 이해해야만 Business Capabilities 를 챙길 수 있다.
- 결국은,, Business Capabilities !!