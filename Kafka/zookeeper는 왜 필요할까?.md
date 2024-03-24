### zookeeper는 왜 필요할까?

Zookeeper는 Apache 재단에서 초기 Hadoop의 하위 프로젝트로 개발되었던, 분산 시스템 코디네이터

기능 확장에 다라서, 분산 시스템에서 공통적으로 겪는 문제를 해결하는 코디네이터로서 프로젝트 분리

**Kafka cluster또한 분산시스템으로서 Zookeeper 필요**

- Cluster 내에서 어떤 Broker가 Leader 인지(Controller 선정)
- 어느 Broker 가 Partition/Replica 리더를 선출할 지 선정
- Topic의 파티션 중에 어느 것이 Leader partition인지 정보 저장
    - 장애 시, 어느 파티션이 우선되는지 정보 관리
- Cluster 내 Broker들의 메시징 Skew(데이터가 분산되어있는 정도) 관리/ Broker 내 Partition들의 Skew 관리 등 코디네이터 역할 수행

Kafka와 zookeeper는 의존적이게 동작