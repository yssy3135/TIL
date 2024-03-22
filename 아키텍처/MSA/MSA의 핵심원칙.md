# MSA의 핵심원칙

### MSA의 핵심 원칙

마틴 파울러(Martin Fowler)가 정의한 MSA의 특징

- Componentization via services

**Business Capabilities**

- 고객의 니즈를 달성하고 빠르게 변화하는 산업구조에 맞춰서 우리의 팀과 구조를 맞추는것.
- 목적조직 목적을 가지고 그것을 해결하는것

**목적조직**

특정 모적을 가진 사내 조직으로써 명확한 미션과 비전을 가지고 팀의 모든 의사결정이 진행

### Product, Not Project

Project → 1회성

달성 이후에는 유지보수 조직으로 이동

내부적으로(In house) 개발과 유지

- 빠른대응을 통해 Business Capabilities를 만족함으로써 내부에서 개발하고 운영하는데 드는 비용보다 더 많은것을 벌어들일 수 있다.

**Smart endpoints and dumb pipes**

- RESTful 같은 최대한 단순한 방식의 프로토콜 사용
- Enterprise Service Bus is Smart Pipe(routing, transformation, business rule, …)
    - Smart pipe는 dumb pipe가 되어야 한다!
        - 파이프는 바보가 되어야한다 최대한 단순해야한다.
    - 단순하다 → 통신에 집중한다.온전히 통신 프로토콜을 잘 구축하는데 집중해야 한다.
- 관심의 분리 - Separation of Concerns
  

**Decentralized Data Management / Governance**

DB의 분리

- 데이터의 유연성, 탄련성의 확보
- 비즈니스 필요성에 따른 최적의 기술스택 사용.

**Infrastructure Automation**

- CI/CD 파이프라인과 같은 인프라 자동화의 중요성
- 지속적인 통합 / 지속적인 배포

필요한 일이지만 지금까지 힘들었다 (모놀리식)

모놀리식 → Business Capability가 그렇게 중요하지 않았다.

현대에는 Business Capability 가 너무나도 중요해졌고
msa아키텍처가 잘맞고 적극적으로 잘 사용하기 위해서
CI/CD 파이프라인을 비롯한 각종 인프라 아키텍처들의 수동적이고 기계적인 작업들이 **자동화**되어야 다른 비즈니스 로직의 구현에 집중할 수 있다.

### Design for Failure

- 시스템은 언제든지 문제가 생길 수 있다.
- 인정하고 감지하고 복구하는데 항상 염두가 된 설계를 해야 한다.
- 감지
    - e.g. Circuit Breaker
- 복구
    - e.g. Container Ochestration, K8S
- 의도치 않은 결과 방지
    - e.g. Transaction,Event Driven
- 서비스 간의 영향도
    - e.g. Chaos Test