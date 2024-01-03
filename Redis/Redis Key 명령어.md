# Key 명령어

### TTL (Time To Live)

- EXPIRE [KEY] [SECOND]
    - 모든 키의 삭제 시간 설정 가능
- TTL [KEY]
    - 남은 시간 확인

### DEL

- DEL [KEY]
    - key 삭제

### UNLICK

- UNLICK [KEY]
- DEL과 달리 비동기적으로 삭제
    - 내부적으로 지워야할 Element의 개수가 많은경우 동기적인 삭제를 진행하는데 시간이 꽤 소요될 수 있다.
    - 삭제 명령 뒤에 대기하고 있던 명령들을 처리하지 못하고 응답지연이 일어남.
    - 별로 스레드로 삭제 절차 진행

### MEMORY USAGE

- MEMORY USAGE [KEY]
    - 해당 키의 데이터 크기를 확인 가능