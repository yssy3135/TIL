### Kafka란?

- 실시간 이벤트 스트리밍 플랫폼.
- LinkedIn에서 겪었던 다양한 어려움 (확장 용이, 고성능 실시간 데이터 처리)
    - e.g. 채팅, 피드 등에서 필요한 **‘실시간성’**
- 고가용성, 고성능
    - 기존의 Sync 방식을 대체’가능’할 수도 있는 수단
- 일반적으로 규모가 작은 비즈니스에는 비적합
    - 일단 큐에 넣어두고, 필요에 따라 나중에 처리해도 되는 비즈니스
    - 라우팅 변경에 대한 가능성이 굉장히 큰 비즈니스
        - 오히려 간단한 구조의 RabbitMq가 적합 할 수 있다.

### 왜 Kafka를 사용하지?

- **대규모의 실시간 이벤트/데이터 스트리밍이 필요한 비즈니스에 적합.**
- 대규모 지원 → 소규모도 지원 → 모두다 kafka를 쓰면 되지 않을까?
    - 하지만 적합하지 않을 수 있다.  → OverSpec
    - 러닝 커브가 높다, Kafka 전문 관리 인력 필요

### kafka의 기본 개념

![Kafka란_image_1.png](images%2FKafka%EB%9E%80_image_1.png)

**Producer**

- 메시지를 발행하는 **‘주체’**
- Produce: 메시지를 방행하는 동작

**Consumer**

- 메시지를 소비(즉, 가져와서 처리) 하는 ‘주체’
- Consume: 메시지를 가져와서 처리하는 동작

**Consumer Group**

- 메시지를 소비(즉, 가져와서 처리) 하는 **‘주체 집단’**
- Consumer Group 끼리는 Topic의 데이터를 공유하지 않음.

**Kafka Cluster**

**Producer**

- Kafka Cluster에 존재하는 Topic에 메시지를 발행 하는 ‘주체’
- Produce: 메시지를 Kafka Cluster에 존재하는 Topic에 발행하는 동작

**Consumer**

- Kafka cluster에 존재하는 Topic에서 메세지를 소비(가져와서 처리) 하는 **‘주체’**
- Consume: 메시지를 Kafka Cluster에 존재하는 Topic으로부터 가져와서 이를 처리하는 동작

**Kafka Broker**

**Producer**

- Kafka Cluster 내 Topic이 포함된 Kafka Broker에 메시지를 발행하는 ‘주체’
- Produce: 메시지를 Kafka Broker 내 Topic에 발행하는 동작

**Consumer**

- Kafka Cluster내 Topic이 포함된 Kafka Broker로부터 메시지를 소비(가져와서 처리) 하는 ‘주체’
- consume: 메시지를 Kafka Cluster에 존재하는 Topic으로부터 가져와서 이를 처리하는 동작

**Zookeeper**

**Apache Zookeeper (for hadoop Ecosystem)**

- 분산 처리 시스템에서 분산 처리를 위한 코디네이터
    - 누가 리더인지, 어느 상황인지, 동기화 상태등 관리
- **KafkaCluster를 관리**
- Kafka Broker의 상태를 관리
- Cluster내에 포함된 Topic 관리
- 등록된 Consumer 정보를 관리하는 ‘주체’

### Kafka Topic

- 메시지를 발행하고 소비할 수 있는 **객체(Obejct)**
    - ‘~토픽에 Produce된 메시지를 Consume 하여 처리한다’
- Kafka Topic은 고가용성, 고성능을 구현하는 핵심 개념
- Topic에 존재하는 수 많은 설정값들에 따라 성능, 가용성에 엄청난 차이를 불러올 수 있다.

**Kafka Topic 내 설정들이 어떤 것을 의미하는지 이해하고 판단 할 수 있어야 한다.**

### Partition

![Kafka란_image_2.png](images%2FKafka%EB%9E%80_image_2.png)

- 메시지를 발행/소비 하는 객체, 수많은 설정 값들이 존재
- Topic은 하나 이상의 partition으로 이루어짐
- partition이란 하나의 토픽에 포함된 메세지들을 물리적으로 분리하여 저장하는 저장소
    - 하나의 메시지를 분리시켜 저장하는 것이 아닌, 하나의 메시지가 하나의 파티션에 들어가는 형태
    - 많이 분리 → 많은 물리적 리소스 활용가능
    - Partition이 많으면 성능이 향상된다.
    - **파티션 → 성능과 직결되는 요소**

**파티션 개수와 성능**

- 하나의 Partition은 하나의 Kafka Broker에 소속됨 (1:1)
- 하나의 kafka Broker는 1개 이상의 Partition을 가지고 있음 (1:N)
- Partition 갯수가 많다고 해서, Kafka Broker 갯수가 많은 것을 의미하지는 않는다.
    - 즉, Partition 갯수가 절대적인 성능 결정 요소는 아님.

**Partition 과 Partition Replica**

- 파티션의 Replication Factor는 가용성을 위한 개념
- 하나의 파티션은, Kafka Cluster 내에 1개 이상의 복제본(Replica)를 가질 수 있다.
    - RF(Replication Factor)는 복제본의 갯수를 의미
    - 즉, RF 가 1보다 큰 수치를 가져야만 고가용성을 달성할 수 있다.
        - 일반적으로 클 수록, 가용성이 높다고 할 수 있다.
        - Broker 갯수가 충분할 때 한정
    - 너무 크면 메시지 저장 공간을 낭비할 수 있다.
    - Produce 할때도 지연 시간이 길어질 수 있다.

**ISR, In-sync Replica (Sync 가 되었다고 판단 가능한 레플리카 그룹)**

- Partition의 복제본이 많아지면 (RF가 커지면) 가용성이 늘어난다, 또한 Produce할 때도 복제해야할 데이터가 늘어난다.
    - Produce시 지연 시간이 길어질 수있다.

**지연 시간을 짧게 유지하고 싶다면?**

ISR(In-sync Replica) 그룹 이란, 하나의 파티션에 대한 Replica 들이 동기화 된 그룹을 의미.

- ISR 그룹에 많은 파티션 포함
    - Produce 신뢰성/가용성 향상, 지연시간 증가
- ISR 그룹에 적은 파티션 포함
    - Produce 신뢰성/가용성 하락, 지연시간 감소

적절하게 토픽에 Produce 되었다?

- 토픽 내 파티션의 모든 ISR 그룹에 복제 되었다

### Kafka Topic 설계시 유의할 것들

- 토픽에 포함된 패티션 갯수(Partition Number)
    - Topic을 사용하기 위한 일꾼들
    - 일반적으로 퍼포먼스와 연관
- 토픽에 포함된 파티션의 복제본 갯수 (RF, Replication Factor)
    - Produce시, 정상적으로 ‘Replication’ 되었다.
    - 일반적으로 가용성과 연관
- 토픽의 ISR 그룹에 포함된 파티션 그룹 (ISR, In-Sync- Replica)
    - replica들이 정상적으로 ‘Sync’ 되었다.
    - 일반적으로 Produce시의 지연시간, 신뢰성과 연관


### 결론

- Kafka는 대규모 이벤트/데이터 스트리밍(실시간성) 플랫폼
- 실시간성이 충분하지 않은 비즈니스는 굳이 Kafka를 사용할 필요 없다
- 대규모 분산 처리 환경에서 적합한 선택지
- 높은 러닝 커브, 카프카 클러스터 운영을 위한 전문 인력들의 필요성
- 잘 사용한다면 신뢰성/가용성/성능면에서 최고의 효율

**토픽을 이루는 파티션과 관련된 설정들의 ‘이해’ 필요**

비즈니스 특성에 따라, 인프라 환경에 따라서 적절한 설정값들을 ‘판단’ 필요