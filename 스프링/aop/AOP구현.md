@Aspect를 사용 하려면 @EnableAspectJAutoProxy를 스프링 설정에 추가해야 하지만, 스프링 부트를사용하면 자동으로 추가된다.

```java
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;

@Slf4j
@Aspect
public class AspectV1 {

		//hello.aop.order 패키지와 하위 패키지
		@Around("execution(* hello.aop.order..*(..))")
		public Object doLog(ProceedingJoinPoint joinPoint) throws Throwable {

			log.info("[log] {}", joinPoint.getSignature()); //join point 시그니처
          return joinPoint.proceed();
    }
}
```

- @Around 애노테이션의 값인 execution( * hello.aop.order..*(..)) 은 포인트컷이 된다.
- @Around 애노테이션의 메서드인 doLog는 어드바이스(Advice)가 된다.

스프링 AOP는 AspectJ의 문법을 차용하고, 프록시 방식의 AOP를 제공한다.

AspectJ를 직접 사용하는 것이 아니다.

스프링 AOP를 사용할 때는 @Aspect애노테이션을 주로 사용하는데, 이 애노테이션도 AspectJ가 제공하는 애노테이션이다.

@Aspect는 애스펙트라는 표식이지 컴포넌트 스캔이 되는 것은 아니다. 빈으로 등록해주어야 한다.

### 포인트컷 분리

@Around에 포이트컷 표현식을 직접 넣을 수도 있지만, @Pointcut애노테이션을 사용해서 별도로 분리할 수 있다.

```java
@Pointcut("execution(* hello.aop.order..*(..))") //pointcut expression private void allOrder(){} //pointcut signature
private void allOrder(){}

@Around("allOrder()")
public Object doLog(ProceedingJoinPoint joinPoint) throw Throwable {}
```

********@Pointcut********

- @Pointcut에 포인트컷 표현식을 사용한다.
- 메서드 이름과 파라미터를 합쳐서 포인트컷 시그니쳐라 한다.
- 메서드 반환 타입은 void여야 한다.
- 코드 내용은 비워둔다.
- @Around 어드바이스에서는 포인트컷을 직접 지정해도 되지만, 포인트컷 시그니처를 사용해도 된다.
- private, public 같은 접근 제어자는 내부에서만 사용하면 private를 사용해도 되지만, 다른 애스팩트에서 참고하려면 public 를 사용해야 한다.

- 분리하면 하나의 포인트컷 표현식을 여러 어드바이스에서 함께 사용할 수 있다.
- 다른 어드바이스에서도 포인트컷을 함께 사용할 수 있다.
- *Service, Servi* 와 같은 패턴도 가능하다.
- 클래스 인터페이스에 모두 적용된다.

`@Around("allOrder() && allService()")`

포인트컷은 조합할 수 있다. &&(AND), ||(OR), !(NOT) 3가지 조합이 가능하다.

### 포인트 컷 참조

포인트컷을 공용으로 사용하기 위해 별도의 외부 클래스에 모아두어도 된다.

외부에서 호출할 때는 포인트컷의 접근 제어자를 public 으로 열어두어야 한다.

```java
@Around("hello.aop.order.aop.Pointcuts.allOrder()")
public Object doLog(ProceedingJoinPoint joinPoint) throws Throwable {}

@Around("hello.aop.order.aop.Pointcuts.orderAndService()")
public Object doTransaction(ProceedingJoinPoint joinPoint) throws Throwable{}
```

사용법은 패키지 명을 포함한 클래스 이름과 포인트컷 시그니처를 모두 지정하면 된다.

포인트컷을 여러 어드바이스에서 함께 사용할 때 이 방법을 사용하면 효과적이다.

### 어드바이스 순서

- 어드바이스는 기본적으로 순서를 보장하지 않는다.
- 순서를 지정하고 싶으면 @Aspect적용단위로 org.springframework.core.annotation.@Order 애노테시션을 적용해야 한다.
- 문제는 이것을 어드바이스 단위가 아니라 **클래스단위로 적용**할 수 있다는 것.
- **하나의 애스펙트에 여러 어드바이스가 있으면 순서를 보장 받을 수 없다.**
- 따라서 애스펙트를 별도의 클래스로 분리해야 한다.

![img_20.png](img%2Fimg_20.png)

### 어드바이스 종류

- @Around: 메서드 호출 전후에 수행, 가장 강력한 어드바이스, 조인 포인트 실행 여부 선택, 반환 값 변환, 예외 변환 등이 가능
    - 조인 포인트 실행 여부 선택 joinPoint.proceed() 를 호출해야 다음 대상 호출
    - proceed()를 여러번 실행할 수도 있다.
    - 호출하지 않으면 대상이 호출되지 않음
    - 전달 값 변환
    - 반환 값 변환 joinPoin.proceed(args[])
    - 예외 변환
    - 트랜잭션 처럼 try~ catch~ finally 모두 들어가는 구문 처리 가능
    - 첫번째 파라미터는 ProceedingJoinPoint를 사용해야한다.
- @Before : 조인 포인트 실행 이전에 실행
    - 메서드 종료시 자동으로 다음 타겟이 호출
- @AfterReturning : 조인 포인트가 정상 완료후 실행
    - returning 속성에 사용된 이름은 어드바이스 메서드의 매개변수 이름과 일치해야 한다.
    - returning 절에 지정된 타입의 값을 반환하는 메서드만 대상으로 실행한다 (부모 타입을 지정하면 도믄 자식 타입은 인정된다.)
    - @Around와는 다르게 반환되는 객체를 변경할 수 없다.
- @AfterThrowing : 메서드가 예외를 던지는 경우 실행
    - throwing 속성에 사용된 이름은 어드바이스 메서드의 매개변수 이름과 일치해야 한다.
    - thorowing 절에 지정된 타입과 맞는 예외를 대상으로 실행한다.( 부모타입 지정시 모든 자식타입 인정)
- @After : 조인 포인트가 정상 또는 예외에 관계없이 실행(finally)
    - 정상 및 예외 반환 조건을 모두 처리
    - 일반적으로 리소스를 해제하는 데 사용

> @Around를 제외한 나머지 어드바이스들은 @Around가 할수 있는 일의 일부만 제공한다.
@Around 어드바이스만 사용해도 필요한 기능을 모두 수행할 수 있다.
>

### 참고 정보 획득

- 모든 어드바이스는 org.aspectj.lang.JoinPoint를 첫번째 파라미터에 사용할 수 있다.
- @Around는 ProceedingJoinPoint를 사용해야 한다.

**JoinPoint 인터페이스의 주요 기능**

- getArgs() : 메서드 인수를 반환
- getThis() : 프록시 객체를 반환
- getTarget() : 대상 객체를 반환
- getSignature() : 조언되는 메서드에 대한 설명을 반환
- toString() : 조언되는 방법에 대한 유용한 설명을 인쇄.

### @Around 를 사용해서 모두 구현할 수 있는데 왜 @Around 가 존재할까?

- @Around가 가장 넓은 기능을 제공하는건 맞지만 실수할 가능성이 있다.
- @Before, @After같은 어드바이스는 기능은 적지만 실수할 가능성이 낮고, 코드도 단순하다.
- 의도가 명확하게 드러난다.

**제약 덕분에 역할이 명확해진다. 다른 개발자도 이 코드를 보고 고민해야 하는 범위가 줄어들고 코드의 의도도 파악하기 쉽다.**