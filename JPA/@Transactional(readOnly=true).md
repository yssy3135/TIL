# @Transactional(readOnly = true)는 뭐고 어디에 사용할까?

```java
/**
     * A boolean flag that can be set to {@code true} if the transaction is
     * effectively read-only, allowing for corresponding optimizations at runtime.
     * <p>Defaults to {@code false}.
     * <p>This just serves as a hint for the actual transaction subsystem;
     * it will <i>not necessarily</i> cause failure of write access attempts.
     * A transaction manager which cannot interpret the read-only hint will
     * <i>not</i> throw an exception when asked for a read-only transaction
     * but rather silently ignore the hint.
     * @see org.springframework.transaction.interceptor.TransactionAttribute#isReadOnly()
     * @see org.springframework.transaction.support.TransactionSynchronizationManager#isCurrentTransactionReadOnly()
     */
    boolean readOnly() default false;
```

- 트랜잭션이 읽기 전용인 경우 true 값으로 설정하는 플래그.
- 런타임 시 해당 트랜잭션을 최적화.
- 트랜잭션 하위 시스템에 대한 힌트 역할.
- 반드시 쓰기 액세스 시도 실패를 야기하지 않음.
- 읽기 전용 힌트를 해석할 수 없는 트랜잭션 매니저는 예외를 던지지 않고 힌트는 무시

## 어떤 역할을 할까?

1. 변경감지(dirty check) 수행 안함.

   ****************************변경감지란?****************************

    - Entity를 조회하게 되면 그 당시의 상태에 대한 Snapshot을 저장한다.
    - 트랜잭션이 커밋될 때 초기 상태의 snapshot과 커밋지점의 Entity의 상태를 비교하여 변경된 내용에 대해 update 쓰기 지연 저장소에 저장한다.
    - 일괄적으로 쓰기 지연 저장소에 있는 query를 flush하고 commit함으로 update를 명시하여 사용하지 않아도 Entity의 변경이 일어난다.

**변경감지를 수행하지 않으면 뭐가 달라지는데?**

- 변경감지를 위한 snapshot을 따로 보관하지 않기 때문에 메모리가 절약된다

1. flush 모드를 manual로 변경한다.
    - 트랜잭션 내부에서 flush가 자동으로 호출되지 않는다.
    - 명시적으로 flush를 호출하지 않는 한 Entity를 수정해서 DB에 반영되지 않는다.
    - 예상치 못한 수정을 방지 할 수 있다.

2. DB Replication 에서의 부하 분산

   DB Replication이란?

    - DB의 장애를 빠르고 트래픽을 분산하기 위해 실시간 복제 DB를 운용하는 방식
    - 2개 이상의 DB서버가 존재
    - 하나는 Master 나머지는 Slave 역할
    - 쓰기연산은 Master에서 수행
    - 읽기 연산은 Slave에서 수행
        - **readOnly 로 설정된 transaction은 이 SlaveDB에서 처리된다.**

   ![image](https://github.com/yssy3135/TIL/assets/62733005/aa6ba9ea-9d7f-4f0d-b7ff-74453aa7c037)