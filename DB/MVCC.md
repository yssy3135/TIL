# MVCC

# MVCC (Multi Version Concurrency Control)

레코드 레벨의 트랜잭션을 지원하는 DBMS가 제공하는 기능

********목적********

잠금을 사용하지 않는 일관된 읽기를 제공하는 것!

**************어떻게?**************

InnoDB는 언두 로드(Undo log)를 이용해 기능을 구현한다.

**멀티 버전이란?**

하나의 레코드에 대해 여러개의 버전이 동시에 관리된다는 것!

**예를 들어 확인해보자**

![image](https://github.com/yssy3135/TIL/assets/62733005/38c3b7f3-92f0-40da-9f00-e36e1da4e589)

******Insert 실행******

![image](https://github.com/yssy3135/TIL/assets/62733005/483f90e6-6f46-4e05-b1dd-cf440ee7d8f8)

**update 실행**

![image](https://github.com/yssy3135/TIL/assets/62733005/8ca75f39-a1b7-415a-b86d-4d9c11ec052a)


![image](https://github.com/yssy3135/TIL/assets/62733005/3f3415b1-05bf-4279-8cf9-6f59ea8dcb35)



- Update 문자 실행시 커밋여부와 상관없이 버퍼 풀은 새로운 값으로 update
- 디스크 데이터 파일에는 새로운 값으로 업데이트 돼 있을 수도 있고 아닐 수도 있다.

  (InnoDB는 ACID를 보장하기 때문에 일반적으로는 버퍼 풀과 데이터 파일은 동일한 상태라고 가정해도 무방)


***************************************************아직 커밋이나 ROLLBACK이 되지 않은 상태에서 조회한다면?***************************************************

![image](https://github.com/yssy3135/TIL/assets/62733005/2327def7-8ba1-4fa4-8480-dc3c097cb799)

- MySQL 서버의 시스템 변수(transaction_isolation)에 설정된 격리수준 에 따라 다르다.
    - READ_UNCOMMITED일 경우 버퍼 풀이 가지고 있는 데이터를 읽어 반환
    - READ_COMMITTED 이거나 그 이상의 격리수준 (REPEATABLE_READ, SERIALIZABLE)인경우

      아직 커밋되지 않았기 때문에 언두로그 영역의 데이터를 반환한다.


### COMMIT 과 ROLLBACK

**COMMIT**

- commit 명령을 실행하면 InnoDB는 더 이상의 변경 작업 없이 지금의 상태를 영구적인 데이터로 저장
- 커밋이 된다고 언두 영역의 백업 데이터가 바로 삭제되지는 않는다
- 언두 영역을 필요로 하는 트랜잭션이 더는 없을때 삭제됨.

**ROLLBACK**

- InnoDB는 언두 영역에 있는 백업된 데이터를 InnoDB 버퍼 풀로 다시 복구하고, 언두 영역의 내용을 삭제