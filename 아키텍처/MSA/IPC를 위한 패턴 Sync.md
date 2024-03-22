**IPC : Inter Process Communication**

- 프로세스간 통신 → 서비스간 통신 → MSA
- Network 통신이 어떻게 이루어지는지 이해가 필요
    - OSI 7계층(Open Systems Interconnection), TCP/IP 모델

![IPC를 위한 패턴 Sync_img_1.png](images%2FIPC%EB%A5%BC%20%EC%9C%84%ED%95%9C%20%ED%8C%A8%ED%84%B4%20Sync_img_1.png)

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