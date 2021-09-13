## 1. The IoC Container

### 1.1 Spring Ioc Container와 Beans 소개

이번장에서는 스프링 프레임워크의 Inversion of Control(IoC, 제어의 역전)을 다룬다.

IoC는 DI(dependency injection)으로도 알려져 있다.

객체가 생성자 인수, 팩토리 메서드에 대한 인수 또는 팩토리 메서드에서 생성되거나 반환된 후

객체 인스턴스에 설정된 속성을 통해서만 객체가 의존성(함께 작업하는 다른 객체)을 정의하는 프로세스 이다.

컨테이너는 빈을 생성할때 의존성을 주입한다.

이 과정은 빈이 자신의 생성이나 의존성에 대해 컨트롤하지 않는 과정이므로 기능적으로 제어의 역전(IoC)인 것이다.

org.springframework.beans 와 org.springframework.context는 스프링 IoC컨테이너의 기본 패키지 이다.

BeanFactory 인터페이스는 모든 타입의 객체를 수용할 수 있는 설정을 제공한다.

ApplicationContext는 BeanFactory의 서브인터페이스로 이것을 제공한다.

- Spring의 AOP기능의 쉬운 통합
- Message resource handling ( 국제화 사용을 위한)
- 이벤트 발행
- 웹 애플리케이션에서 사용되는 WebApplicationContext와 같은 특정 응용 계층 컨텍스트

간단히 말하면 BeanFactory는 설정 프레임워크와 기본 기능을 제공하고 ApplicationContext는 더 많은 엔터프라이즈 기능을 추가한다.

ApplicationContext는 BeanFactory의 완전한 superset(상위집합?)이며 이장에서 SpringIoC컨테이너의 설명에 사용된다.

스프링에서는 애플리케이션에 기반을 두고 IoC컨테이너에 의해 관리되는 객체를 빈이라고 부른다.

빈은 스프링 IoC컨테이너에 의해 응집되고 관리되고 생성되는 객체이다.

빈은 애플리케이션의 수많은 객체중 하나일 뿐이다.

빈들과 그들간의 의존성은 설정 메타데이터에 반영된다.