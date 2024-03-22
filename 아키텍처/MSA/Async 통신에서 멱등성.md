### Async 통신의 한계

- 특정 상황들에서는 필연적으로 두 번 이상 데이터를 받을 가능성이 존재
- Async 통신의 장점이 명확하고, 사용하기에 매우 적절한 경우들이 존재하나 한계가 존재한다;
    - e.g. Logging Pipeline UX 상 빠른 리턴 후 결과는 나중에 보여줘도 되는 케이스

**Logging Pipeline**
Log Write to File -> File Scraping -> Pipeline ..... -> Indexing .... -> Kibana(Elastic Search)

### Async 통신을 활용해서 중요한 비즈니스 로직을 처리할 수 있을까?

- 2번 이상 수신되는 것이 문제

2번 이상 수신되어도, 괜찮을 수 있는 방법이 있을까?

→ 멱등성 (idempotent)이 필요한 상황 식별!

**멱등성이란?**

- 특정 호출이 1번 또는 여러변 호출되어도 서버의 상태는 1번 호출된 것과 동일해야 한다.

일반적으로 Update나 Delete 요청은 멱등성을 가지며, Create 요청은 멱등성을 가지지 않는다.

→ Sync 통신에서는 멱등석을 Business Logic으로 구현

### Async 통신에서의 멱등성

- Async 통신에서는 기본적으로 멱등성이 보장되지 않는 통신 방식
- 멱등성을 구현하기 위해서는, ‘다른 무언가’가 필요하다.

**멱등성 구현을 위한 DB Table(RDB)**

**queue_log**

|  |  |
| --- | --- |
| message_id | 특정 큐잉 메세지의 고유 식별자 |
| status | 메세지의 상태 (대기중, 처리중, 완료) |
| created_timestamp | Producer 로부터 메세지가 큐에 추가된 시간 |
| processed_timestamp | Consumer 로부터 메세지가 큐에서 처리된 시간 |
| processed_status | Consumer 로부터 처리된 메세지의 결과 (성공, 실패) |

### **멱등성이 구현된 Producer, Consumer**

**멱등성 구현 이전,**
Producer 는 항상 메세지를 생성하여 큐에 추가하고, Consumer 는 항상 메세지를 처리해요

**멱등성 구현 이후**
Message Produce 시,

- Produce 전에, queue_log 테이블에 동일한 message_id 가 존재하는지 확인
- 존재할 경우, 이미 수행되었으므로 에러/스킵 처리
- 존재하지 않을 경우, 새로운 메세지를 큐에 추가
  Message Consume 시,
- Consume 전에, queue_log 테이블에서 message_id 의 상태를 확인
- 처리된 경우 혹은 처리중인 status 인 경우, 중복 처리로 간주하여 에러/스킵 처리
- 처리되지 않은 경우, message_id 의 status 값을 처리중으로 변경 후에 메세지 처리
- 성공 시, processed_status 값을 처리 성공으로 처리
- 실패 시, processed_status 값을 처리 실패로 처리

구현 할 수 있다는 예시, 절대로 효율적인 방법이나 방식이 아님.