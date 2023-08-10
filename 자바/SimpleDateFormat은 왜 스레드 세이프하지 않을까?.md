### SimpleDateFormat이란?

- 문자열인 날짜를 Date타입으로 parsing, formatting할때 사용하는 구현 클래스이다.

### Thread-safe란?

- 멀티스레드 환경에서 하나의 공유자원에 여러 스레드가 동시에 접근이 이루어져도 동작에 문제가 없어야 한다
- Thread-safe를 보장해주지 못한다는 것은 동작마다  각각 다른 결과가 도출된다는 것!

### SimpleDateFormat은 왜 Thread-safe를 보장하지 못할까?

![img_1.png](image%2Fimg_1.png)

친절하게 내부에 javadoc으로도 쓰여있다.

- Date formats은 동기화 되지 않는다.
- 각 스레드에 대해 별도의 인스턴스를 생성해서 사용해라.
- 여러 스레드가 동시에 접근할때는 *syncronized* 사용해라

내부적으로 Calendar클래스를 인스턴스화 해서 사용하는데 ***singleton***으로 사용하기 때문이다.
![img_2.png](image%2Fimg_2.png)

### 그럼 어떻게 해야할까?

- SimpleDateFormat을 매번 새로운 객체를 인스턴스화해서 사용해야한다.

  하지만 이경우는, 자원을 많이 차지하고 또 금방 garbage상태가 되어버린다.

- 사용하는 메소드에 *syncronized*를 붙인다?

  한개의 스레드만 접근 가능하도록 막는 방법이다.

  이러면 멀티스레드의 장점을 활용을 못하는데 성능이 저하된다.

- **DateTimeFormatter를 사용하자!**

  Java8부터 DateTimeFormatter를 사용할 것을 권장한다.

  DateTimeFormatter는 Thread-safe하다!