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

![Untitled.png](JPA%20id%20generator%20image%2FUntitled.png)

- save를 하면 isNew 메소드 결과에 따라 persist와 merge로 나눠지게 된다.
    - isNew 로직은 새로운 Entity를 판단하기 위해 아래 전략을 사용
    - id가 null이 아닌경우에 new 라고 판한
- 
![Untitled 1.png](JPA%20id%20generator%20image%2FUntitled%201.png)
- 
- MergeEventListener 에게 fireMerge event를 날림.

![Untitled 2.png](JPA%20id%20generator%20image%2FUntitled%202.png)

### **********************************id를 넣어준 경우**********************************

1. onMerge 로직 안에서 entityState에 따라 분기 DETACHED(준영속) 상태를 타게 됨

![Untitled 3.png](JPA%20id%20generator%20image%2FUntitled%203.png)

1. entityIsDetached 로직 안에서  requestedId를 조회해서 검증

![Untitled 4.png](JPA%20id%20generator%20image%2FUntitled%204.png)

![Untitled 5.png](JPA%20id%20generator%20image%2FUntitled%205.png)

**********************************id를 넣어주지 않을 경우**********************************

1. isNew를 타게 되며 위에서 id 를 넣어줬던 getIdentifier에서는 id를 넣어주지 않았기 때문에 null을 반환한다.

![Untitled 6.png](JPA%20id%20generator%20image%2FUntitled%206.png)

1. entityState에 따라 로직을 동일하게 타게 되는데 이때  id가 존재하는 Entity들은 Detached를 탔지만

   id가 null인 경우는 TRANSIENT(비영속) 상태를 만들어버린다.

![Untitled 7.png](JPA%20id%20generator%20image%2FUntitled%207.png)

![Untitled 8.png](JPA%20id%20generator%20image%2FUntitled%208.png)

2. 따라서 TRANSIENT 분기를 타게 되고 아래 saveWithGeneratedId를 통해 id를 넣어주게 된다.

![Untitled 9.png](JPA%20id%20generator%20image%2FUntitled%209.png)

# 문제해결 과정

- 시도 1

IdentifierGenerator가 인터페이스로 존재하니 구현해서 넣어버리자.


![Untitled 10.png](JPA%20id%20generator%20image%2FUntitled%2010.png)

- generate 할 때 @GeneratedValue(strategy = GenerationType.IDENTITY)로 지정했을 경우
  IdentifierGeneratorHelper.POST_INSERT_INDICATOR 로직을 타면서
  DB sequence를 가져와서 넣어주기 때문에 null이면 IdentifierGeneratorHelper.POST_INSERT_INDICATOR 를 반환하도록 구현했다.

![Untitled 11.png](JPA%20id%20generator%20image%2FUntitled%2011.png)

### 구현

![Untitled 12.png](JPA%20id%20generator%20image%2FUntitled%2012.png)

![Untitled 13.png](JPA%20id%20generator%20image%2FUntitled%2013.png)

![Untitled 14.png](JPA%20id%20generator%20image%2FUntitled%2014.png)

### 다시 문제….

- 하지만  id를 넣어줬을경우는 잘 동작했지만 id 를 넣어주지 않고 저장 하는 경우 IdentifierGeneratorHelper.POST_INSERT_INDICATOR 로직을 타는 시점에서 오류가 발생했다.
- insert를 하는 시점에 identityDelegate란 놈이 insert를 하고 식별자를 검색하는데 **identityDelegate 자체가 null 이었다!**

![Untitled 15.png](JPA%20id%20generator%20image%2FUntitled%2015.png)

- 알고보니 서버를 실행할 때 Entity인 객체들을 모두 읽어와 초기 세팅을 해주는 과정에서

  @GeneratedValue(strategy = GenerationType.IDENTITY) 처럼

  strategy에 따라 Generator를 Entity에 init 하고 있었던것.

  
![Untitled 16.png](JPA%20id%20generator%20image%2FUntitled%2016.png)

### 문제 해결!!

- strategy에 따라 결정되는 generator를 넣어주기 위해  identity를 사용하는 현재 상황게 맞게 IdentityGenerator를 상속
- generate부분만 재정의 해서 원하는 대로 동작하도록 구현.

![Untitled 17.png](JPA%20id%20generator%20image%2FUntitled%2017.png)

![Untitled 18.png](JPA%20id%20generator%20image%2FUntitled%2018.png)

### 회고…

몇줄 안되는 짧은 코드로 설정이 가능했지만 이걸 깨닫기 까지 수많은 디버깅과 시간을 사용했다..

하지만 얻은게 너무 많다.

이제 문제가 생겼을때 어떻게 해결해야 할지 막막함과 제공해주는 기능이 아니라서 포기하는 일은 없을것 같다!

jpa 내부 동작에 대해서도 많이 알게 되었고 많은 자신감을 얻게되었다.