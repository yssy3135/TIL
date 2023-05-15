# 왜??

Data Migration을 진행하면서 id도 동일하게 옮겨지고 싶다.

**요구사항**

- Entity를 저장할때 id 값이 넣어져있으면 해당 값을 그대로 사용
- 입력이 되어 있지 않으면 (null) 이면 DB의 Sequence값을 조회 해 와서 사용

# 문제

- @GeneratedValue(strategy = GenerationType.IDENTITY) 그대로 사용할 경우
  id 값을 넣어줘도 Sequence값을 조회해서 넣어줌.
- 원하는 id 값으로 저장이 가능하도록 해야함

# 분석

1. repository.save()

   ![image](https://github.com/yssy3135/TIL/assets/62733005/874f6ca4-7d39-4106-bb33-8a29f11614b4)

- save를 하면 isNew 메소드 결과에 따라 persist와 merge로 나눠지게 된다.
    - isNew 로직은 새로운 Entity를 판단하기 위해 아래 전략을 사용
    - id가 null이 아닌경우에 new 라고 판한

  ![image](https://github.com/yssy3135/TIL/assets/62733005/e5326501-5839-46a7-ae43-7d7129677bd3)

- MergeEventListener 에게 fireMerge event를 날림.

![image](https://github.com/yssy3135/TIL/assets/62733005/7b6e8d4d-7b38-451b-9e8c-ff30ff250223)

### **********************************id를 넣어준 경우**********************************

1. onMerge 로직 안에서 entityState에 따라 분기 DETACHED(준영속) 상태를 타게 됨

![image](https://github.com/yssy3135/TIL/assets/62733005/7455e727-cc79-42fa-aac3-6830edc2af7b)

1. entityIsDetached 로직 안에서  requestedId를 조회해서 검증

![image](https://github.com/yssy3135/TIL/assets/62733005/012b1a71-bbc0-4c2f-b455-dbe489ccb764)

![image](https://github.com/yssy3135/TIL/assets/62733005/e8c03caa-a58f-4052-8a6f-4118b1166a0f)

**********************************id를 넣어주지 않을 경우**********************************

1. isNew를 타게 되며 위에서 id 를 넣어줬던 getIdentifier에서는 id를 넣어주지 않았기 때문에 null을 반환한다.

![image](https://github.com/yssy3135/TIL/assets/62733005/f0c4bac1-9ae2-457a-9e68-28ee80136424)

1. entityState에 따라 로직을 동일하게 타게 되는데 이때  id가 존재하는 Entity들은 Detached를 탔지만

   id가 null인 경우는 TRANSIENT(비영속) 상태를 만들어버린다.

   ![image](https://github.com/yssy3135/TIL/assets/62733005/83b691ec-22c1-48ad-96bd-df533a295346)

   ![image](https://github.com/yssy3135/TIL/assets/62733005/fa98bb65-045a-4fa8-a5fb-0aa6b72450db)

2. 따라서 TRANSIENT 분기를 타게 되고 아래 saveWithGeneratedId를 통해 id를 넣어주게 된다.

![image](https://github.com/yssy3135/TIL/assets/62733005/c3718eb1-bce0-4b97-9784-f2c594ca378d)

# 문제해결 과정

- 시도 1

IdentifierGenerator가 인터페이스로 존재하니 구현해서 넣어버리자.

![image](https://github.com/yssy3135/TIL/assets/62733005/2c46a94f-a0d4-4403-9bcd-223919479a06)

- generate 할 때 @GeneratedValue(strategy = GenerationType.IDENTITY)로 지정했을 경우
  IdentifierGeneratorHelper.POST_INSERT_INDICATOR 로직을 타면서
  DB sequence를 가져와서 넣어주기 때문에 null이면 IdentifierGeneratorHelper.POST_INSERT_INDICATOR 를 반환하도록 구현했다.

![image](https://github.com/yssy3135/TIL/assets/62733005/57b82d74-bb27-4315-8a61-2274d663fd0b)

### 구현

![image](https://github.com/yssy3135/TIL/assets/62733005/a983802a-5dd5-4cbf-b570-7bd214167993)

![image](https://github.com/yssy3135/TIL/assets/62733005/55665917-49fb-4a4a-9576-caf9b8066caa)

![image](https://github.com/yssy3135/TIL/assets/62733005/6280db6e-2d3e-4433-ac8a-9b4bd98c46df)

### 다시 문제….

- 하지만  id를 넣어줬을경우는 잘 동작했지만 id 를 넣어주지 않고 저장 하는 경우 IdentifierGeneratorHelper.POST_INSERT_INDICATOR 로직을 타는 시점에서 오류가 발생했다.
- insert를 하는 시점에 identityDelegate란 놈이 insert를 하고 식별자를 검색하는데 **identityDelegate 자체가 null 이었다!**

![image](https://github.com/yssy3135/TIL/assets/62733005/9494df07-d348-40f1-85f5-f2fcfc8779f5)

- 알고보니 서버를 실행할 때 Entity인 객체들을 모두 읽어와 초기 세팅을 해주는 과정에서

  @GeneratedValue(strategy = GenerationType.IDENTITY) 처럼

  strategy에 따라 Generator를 Entity에 init 하고 있었던것.

  ![image](https://github.com/yssy3135/TIL/assets/62733005/932bfeaa-4296-4e55-a407-194f1db01acf)


### 문제 해결!!

- strategy에 따라 결정되는 generator를 넣어주기 위해  identity를 사용하는 현재 상황게 맞게 IdentityGenertor를 상속
- generate부분만 재정의 해서 원하는 대로 동작하도록 구현.

![image](https://github.com/yssy3135/TIL/assets/62733005/0bc3a2b4-c033-42f8-99b5-f1842f18bfbc)

![image](https://github.com/yssy3135/TIL/assets/62733005/64289939-7a89-4f46-bc22-aab34c980674)

### 회고…

몇줄 안되는 짧은 코드로 설정이 가능했지만 이걸 깨닫기 까지 수많은 디버깅과 시간을 사용했다..

하지만 얻은게 너무 많다.

이제 문제가 생겼을때 어떻게 해결해야 할지 막막함과 제공해주는 기능이 아니라서 포기하는 일은 없을것 같다!

jpa 내부 동작에 대해서도 많이 알게 되었고 많은 자신감을 얻게되었다.