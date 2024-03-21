# 분해로 생긴 문제들_IPC

### 분해로 인해 달리진 것들

1. 모듈간 통신 → 서비스(프로세스간 통신)
2. Method Call → Network (http,grpc)

**발생 가능한 문제들**

- 요청에 대한 처리량(throughput)이 급격히 하락
    - 모놀리스에서는 성능이 좋은 서버를 사용 (하나의 프로세스, 하나의 서버), 서버 스펙 상승
    - 마이크로는  모놀리스에서 사용하던 서버보다는 작은 서버를 사용할 수 밖에 없었을것.( 합리적, MSA 목적과 일치)
    - 예측이 가능하겠지만, 특정 서버가 리소스를 많이 사용하고 있을 수 있다 , 반대로 예상보다 리소스를 적게 사용해 낭비가 있을수 있다.
    - 각 서비스의 정확학 필요한 컴퓨팅 리소스 파악이 어렵다
    - 즉 성능최적화 예측이 굉장히 어렵다 → 성능 하락 가능성


**특정 서비스의 비즈니스 도메인 특성을 프로그래밍과 연계하여 정확히 파악.**

→ 어느 서비스에서 얼마나 자원이 필요할지 파악

- 필요한 컴퓨팅 자원의 최적화 어렵다 → 성능 하락 가능성
- http, grpc 프로토콜 상의 이슈로 디버깅이 어렵다.
    - Connection pool 관리
    - if JVM, 한정된 리소스로 JVM 최적화가 더 어려워짐.
        - 의도치 않은 겨로가 발생 가능성
    - http, grpc 프로토콜에 디펜던시가 있는 지식 필요
        - Conn Timeout, tpc-3handshake 이해, grpc code..

**프로토콜 및 통신과 관련된 프로그래밍 및 언어 기반 지식**

**문제들을 실제로 경험해보고 대용량 MSA 환경에서 디버깅 경험**

**모니터링 방식의 변경**

- 정혁화된 로그, 메트릭 수집 방식
    - 모놀리식 환경
        - Node Exporter를 활용하여, Node(DB) 상태에 집중
    - MSA
        - Node 상태 뿐만이 아니라, 서비스 간 호출 상태 파악
        - Latency 증감 확인 ,Call 증감 확인
        - 어떤 API 호출중인지 확인
        - Async 방식 등에 대해서도 모니터링 필요
            - 카프카 큐잉
        - 다양한 비즈니스 메트릭에 대해서도 어려워짐

**발생 가능한 문제들2**

- hop 이 늘어남에 따라, 어디서 어떤 요청에 대한 문제가 발생했는지 어려움
    - 서비스의 갯수가 늘어가면서, 어느 요청에서 문제가 발생했는지?

**Tracing**

- 로그 수집을 위한 별도의 인프라 필요 및 관리 필요성
    - 비교적 간단한 로깅 파이프라인
    - 굉장히 복잡하고, 처리해야할 많은 문제들 발생(e.g. 누락)

**Logging**

- 수십, 수백개의 서비스에 대해 JVM/하드웨어 얼마나 정상적인지 지속적인 확인 필요
    - 서비스 또는 서버에 문제가 생기더라도 적절하게 문제 감지 어려움

**Metric, Alert**

- 어느 서버에서 어느 서비스로 호출이 되어야 하는지, 이게 정상인지 확인 필요
    - 서비스간 호출 상태가 옳은 상태인지, 엉뚱한 서비스를 호출하지는 않는지 파악 어려움

**Service-Mesh**

### 분해로 인해 생긴 다양한 문제들을 어떻게 해결할까?

한정된 갯수의 서버와 서비스(프로세스)의 갯수

→ 수 많은 관리해야할 서버와, 수 많은 서비스들을 어떻게 배포하고 관리할까?  → Container (docker)

→ 많은 컨테이너는 또 어떻게 관리하지? → Container Ochestration (k8s)

**k8s, Docker(Containerizing)을 이용한 컨테이너 오케스트레이션**

### MSA 환경에서의 모니터링

**모니터링 (로깅, 메트릭, 얼럿, 트레이싱, 서비스 메시)**

올바른 사용 예

1. 특정 Metric에 사전 정의 되어진 **Alert** 를 통해서, 문제를 감지 /// 확인이 필요한 **상황 발생**
   ( e.g. DB 정합성 문제 발생 감지 )
2. 어떤 Metric에 대한 문제인지를 파악하고, 어떤 행동을 해야할 지 판단

   2-1. Metric 수치를 보고 오판단, 혹은 당장 이슈의 정도가 미미하다면 Alert 조건 등을 수정. 코드 수정

   2-2. 어플리케이션 레벨의 문제라고 판단 시, **Log** 를 확인

   2-2-1. 단순 로그로 확인이 어려울 시, 해당 트랜잭션에 대한 **Tracing** 확인

   2-3. 인프라 수준의 문제라면, 정확한 원인 파악 필요 (e.g. CI/CD 과정에서의 문제, OOM Killed 등)

   2-3. 라우팅, 트래픽 등에 문제라면, **Service-Mesh** 를 통해 현재 서비스 간 트래픽 모니터링 상태 확인

3. 같은 문제가 발생하지 않도록 조치 필요 (회고)

### 로깅(Logging)

- 수 많은 서버(IDC, Cloud) 내의. 수믾은 서비스들의 로그들을 적절히 필터링 하여 누락 없이 로그 저장소 까지 전송해야 한다.
- 수 많은 로그들을 적절히 인덱싱하여, 필요 시 빠르게 다양한 조건으로 검색이 가능해야 한다.
- 
![분해로 인한 문제 IPC_1.png](images%2F%EB%B6%84%ED%95%B4%EB%A1%9C%20%EC%9D%B8%ED%95%9C%20%EB%AC%B8%EC%A0%9C%20IPC_1.png)

- 1차적으로 Disk에 쌓임
- Disk에 쌓여있는 log들을 원하는 필터링 조건에 맞게 수집 (Logstatsh, fluentbig, fluentd)
- kafka(queue) 들을 통해 elastic search 에 전달
- elastic search는 받은 log들을 indexing
- kibana를 통해 log들을 조회 할 수 있도록 제공

### 메트릭, 얼럿(Metric, Alert)

- 수많은 서버(IDC, Cloud)내의, 수많은 서비스 내 메트릭들을 안정적으로 수집하고, 시계열 방식으로 저장
    - RDB가 아닌 시계열 DB를 사용해야 한다. (Prometheus, InfluxDB)
    - 시계열 DB란 시간에 흐름에 따라 데이터를 조회할 수 있는것에 최적화 된 저장방식을 가진 DB
- 다양한 오픈 소스의 메트릭을 지원하는 대시보드를 사용해야 함
- 원하는 복잡한 형태의 비즈니스 메트릭을 작성하고(Query), 이를 기반으로 적절하게 Alert 조건을 설정할 수 있어야 한다.
- 
![분해로 인한 문제 IPC_2.png](images%2F%EB%B6%84%ED%95%B4%EB%A1%9C%20%EC%9D%B8%ED%95%9C%20%EB%AC%B8%EC%A0%9C%20IPC_2.png)

- polling, pull방식으로 metic을 수집 (promethus)
    - metric을 수집하는 주체는 promethus가 되어야함
    - metic을 줄 수 있는 환경 제공(서버), 수집하고 저장하는 것에 집중(promethus), → 관심사 분리


### 트레이싱(Tracing)

- 하나의 소스(트랜잭션)에 대해서, 여러개의 서비스에서 어떤 과정들을 거쳐 수행되었는지 확인해야 함.
    - 적절한 샘플링과, 보기좋은 UI등을 제공해야함.
    - 
![분해로 인한 문제 IPC_3.png](images%2F%EB%B6%84%ED%95%B4%EB%A1%9C%20%EC%9D%B8%ED%95%9C%20%EB%AC%B8%EC%A0%9C%20IPC_3.png)


**Jaeger 트레이싱 아키텍처**

![분해로 인한 문제 IPC_4.png](images%2F%EB%B6%84%ED%95%B4%EB%A1%9C%20%EC%9D%B8%ED%95%9C%20%EB%AC%B8%EC%A0%9C%20IPC_4.png)

### 서비스 메시(Service Mesh)

- 어느 서비스가 어느 서비스를 호출하고 있는지, 어디로 트래픽이 어느 정도로 발생하고 있는지 등을 모니터링 할 수 있어야 한다.

![분해로 인한 문제 IPC_5.png](images%2F%EB%B6%84%ED%95%B4%EB%A1%9C%20%EC%9D%B8%ED%95%9C%20%EB%AC%B8%EC%A0%9C%20IPC_5.png)

### 결론

‘분해’ 라는 액션으로 생간 문제 → MSA Pattern이 해결하고자 하는 문제

원인: 분해

결과: 다양한 문제들

해결법: MSA Pattern, 패턴을 구현한 다양한 기술 스택들 .

구체적으로 언제, 어떤 상황에 어떤 통신 패턴(IPC, Inter Process Communication Pattern)을 활용할지?



# IPC를 위한 패턴 -Sync 패턴

**IPC : Inter Process Communication**

- 프로세스간 통신 → 서비스간 통신 → MSA
- Network 통신이 어떻게 이루어지는지 이해가 필요
    - OSI 7계층(Open Systems Interconnection), TCP/IP 모델

![분해로 인한 문제 IPC_6.png](images%2F%EB%B6%84%ED%95%B4%EB%A1%9C%20%EC%9D%B8%ED%95%9C%20%EB%AC%B8%EC%A0%9C%20IPC_6.png)

**IPC를 위한 패턴**

- Sync (동기) 방식
    - 고객이 느끼는 시간이 긴편 (Latency가 길다)
- Async (비동기) 방식
    - 고객이 느끼는 시간이 짧다 (Latency가 짧다)

### Sync 방식

- (Restful 방식)  HTTP, gRPC 방식을 많이 활용

**적절한 경우**

- 굉장히 중요한 작업을 하는 경우
- 비교적 빠른 작업에 대한 요청일 경우
- 선행작업이 필수적인 비즈니스인 경우

**부적절한 경우**

- 매우 복잡하고 리소스 소모가 많은 작업의 요청일 경우
- 비교적 한정된 컴퓨팅 리소스를 가지고 있는 경우

### Async 방식

- Queue를 활용하여 Produce, Consume 방식으로 데이터 통신을 함
- e.g. rabbitmq, kafka, pubsub…

**적절한 경우**

- 매우 복잡하고 리소스 소모가 많은 작업의 요청일 경우
- 비교적 한정된 컴퓨팅 리소스를 가지고 있는 경우
    - 그런데, 서버 리소스로 인해 누락이 되면 안되는 경우
- 독립적으로 실행되는 수 많은 서비스들이 있는 대용량 MSA 환경
    - 응답 대기시간을 최소화
    - 느슨하게 결합도를 낮춰, 개별 서비스의 확장성을 유연하게 처리
    - 각 서비스에 문제가 생겼더라도, 복구 시에는 데이터 안정적으로 처리 가능

### Sync IPC 패턴  HTTP

OSI 7 응용 계층의 통신 프로토콜로서, L4 계층에서는 tcp 방식을 활용하는 프로토콜
여러가지 종류의 메서드가 존재하지만 일반적으로 4개의 메서드를 활용해요. (CRUD)

- GET
    - 리소스를 얻어오기 위한 메서드 (Read)
- POST
    - 리소스를 변경 (생성) 하기 위한 메서드 (Create)
- PUT
    - 리소스를 변경 (생성된 리소스를 변경) 하기 위한 메서드 (Update)
    - **멱등성(Idempotence) 필수**
        - 호출 횟수와 상관없이 서버의 상태가 같다면 멱등성을 가지고 있다고 할 수 있다.
- DELETE
    - 리소스를 삭제하기 위한 메서드 (Delete)

### Sync IPC 패턴  gRPC

gRPC (google Remote Proedure Call)

- Protocol Buffer라는 것을 기반으로 하는 **원격 프로시저 호출 프레임워크**
    - 사전에 모든 데이터 Spec이 동일함을 약속
    - 약속을 .proto 라는 파일로 표현
        - 서버와 서버간에 어떤 spec을 가지고 소통할 것인지를 정의
- 일반적으로 Server to Server Call 경우에 한해서 사용
- 정확히 특정 계층의 프로토콜이라고 하기는 어렵다 (L4 ~ L7)
- 빠르지만, 번거로운 작업들과 위험이 수반된다.



# IPC를 위한 패턴 -Async

### Async 방식

- Queue를 활용하여 Produce, Consume 방식으로 데이터 통신
- 큐를 활용하는 방식을 표준화한 프로토콜도 존재

**MQTT,AMQP**

OASIS(Organization for the Advancement of Structured Information Standards)
라는곳에서 표준으로 제정한 표준 프로토콜

MQTT (Message Queuing Telemetry Transport)

- 경량 메세징 프로토콜로서, IoT 등 경량화가 최대 목적인 프로토콜
- Publish, Subscribe, Topic 모델 사용

AMQP (Advanced Message Queuing Protocol)

- 엔터프라이즈 레벨의 메세징 시스템을 위한 프로토콜
- MQTT 개념 외에도, Exchange, Binding 등 개념 추가
- Rabbit MQ, Active MQ, ...

### Kafka

- Kafka는 링크드인에서 개발한 대용량의 데이터 스트림을 처리하기 위한 분산 스트리밍 플랫폼
- MQTT, AMQP 등의 표준을 구현하는 프로토콜이 아닌 독립적인 데이터 스트리밍 플랫폼
- 비교적 복잡한 개념
- 성능 (메시지 처리량) 은 압도적으로 좋음
- 고성능 고용량 메시지 처리를 위해 설계
- JVM과 좋은 궁합 상당히 폭넓게 사용
- 러닝 커브가 높다.

**Kafka의 구성요소**

![분해로 인한 문제 IPC_7.png](images%2F%EB%B6%84%ED%95%B4%EB%A1%9C%20%EC%9D%B8%ED%95%9C%20%EB%AC%B8%EC%A0%9C%20IPC_7.png)

Topic

- Producer, Consumer의 메시징 객체

Partition

- Topic을 물리적으로 분할하고, 처리

Broker

- Kafka 클러스터의 각 노드를 의미

Zookeeper

- Kafka 클러스터의 메타데이터 관리

Async 통신의 단점

- 메시지가 유실될 가능성은 거의 없다
- 하지만 2번이상 소비될 가능성이 있다