# Redis란?

**Remote Dictionary Server**

- in-memory
- key-value

### in-memory database

- 빠른 응답속도 및 높은 처리율을 위해
- 디스크가 아닌 메모리에 데이터 적재하고 활용
- 일반적으로 메모리는 디스크 보다 비쌈 또한 디스크만큼 많은 데이터를 저장하는 용도로는 적합하지 않음

**Persistent on Disk**

RDB(Redis DataBase)

- 특정시점에 전체 DB를 디스크에 저장
- 데이터베이스 백업방식
- 한시간이나, 일일단위 특정 시점에 맞취서 백업
- 실행 주기가 길기 때문에 그 사이에 문제가 일어나면 그 시간만큼 추가,변경내용이 유실
- 스냅샷 과정 자체가 fork 기반으로 진행 → 메모리 사용률 급등 할 수 있음 (모니터링 반드시 필요)

AOF(Append Only File)

- 데이터 쓰기 명령어를 적재하는 방식
- RDB 보다는 복구에 용이
- 하지만 그만큼 데이터 용량을 더 많이 활용 디스크에 쓰기 작업이 더 빈번하기 발생  성능에 영향을 끼칠 수 있음.

## Data types

Key Value Store → key에 대해 어떠한 Data를 저장할 것인가?

Strings, Lists, Sets, Hashes, SortedSet

## Single Thread

싱글 스레드 처리

- 외부로부터 동시에 여러 요청을 받을 수 있지만 순차적으로 하나씩 처리
- 내부적으로 Lock을 사용하지 않아도 원자적 실행이 가능 → 데이터 일관성 보장

Redis6.0이상에서는 I/O부분에 다수의 스레드를 설정해서 멀티스레드로 사용이 가능

실제 동작, 명령어 처리 부분은 여전히 싱글스레드로 동작

## 활용사례

### 1.Cache

- 자주 사용하는, 잘 변하지 않는 데이터 Caching

### 2.Session Store

- 세션 분산 저장소로 Redis 활용이 가능
- TTL 이라고 하는 데이터 자동삭제 기능 지원
    - 오래된 Session 데이터를 삭제하고 관리가 용이

### 3.Pub/sub

- Message broker로써 message 발생 pub 구독 sub를 통해 client간 메시지를 주고 받을수 있도록 지원
- 비동기 처리 목적으로 활용가능
- 실시간 채팅과 같이 메시지 중계기능으로도 활용

### 4.Message Queue

- 메시지를 저장하고 해당 메시지를 필요로 하는 서버에서 데이터를 요청한 순서대로 가져가 처리 할 수 있다.

### 5.Geospatial

- 지리공간 데이터를 적재하고 쿼리하는데 활용

### 6.leader board

- 사용자들과 경쟁할 수 있는 점수 체계
- sortedSet 데이터타입를 이용하여 쉽게 구현이 가능