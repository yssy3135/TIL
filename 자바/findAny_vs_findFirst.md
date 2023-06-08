Stream을 직렬로 처리할때 findFirst()와 findAny()는 동일한 요소를 리턴하며,차이점이 없다.

하지만 Stream을 병렬로 처리할때는 차이가 있다.

findFirst()는 여러 요소가 조건에 부합해도 Stream의 순서를 고려하여 가장 앞에 있는 요소를 리턴한다.

반면에 findAny()는 Multi thread에서 Stream을 처리할 때 가장 먼저 찾은 요소를 리턴한다.

따라서 Stream의 뒤쪽에 있는 element가 리터될 수 있다.

아래 코드는 Stream을 병렬parallel() 로 철리할 떄 findFirst()를 사용하는 예제

항상 "b"를 리턴한다.

```java
List<String> elements = Arrays.asList("a", "a1", "b", "b1", "c", "c1");

Optional<String> firstElement = elements.stream().parallel()
        .filter(s -> s.startsWith("b")).findFirst();

System.out.println("findFirst: " + firstElement.get());
```

아래 코드는 Stream을 병렬로 처리할 때, findAny()를 사용하는 예제,

여기서 findAny()는 실행할 때마다 리턴 값이 달라지며 b1, 또는 b를 리턴한다.

```java
List<String> elements = Arrays.asList("a", "a1", "b", "b1", "c", "c1");

Optional<String> anyElement = elements.stream().parallel()
        .filter(s -> s.startsWith("b")).findAny();

System.out.println("findAny: " + anyElement.get());
```

