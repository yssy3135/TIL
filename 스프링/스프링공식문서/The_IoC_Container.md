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

### 1.2. Container Overview

org.springframework.context.ApplicationContext 인터페이스는 스프링 IoC컨테이너를 의미하고

빈들을 생성하고 설정하고 응집시킬 책임이 있다.

컨테이너는 설정 메타데이터를 읽어서 어떤 객체가 생성되고 설정되고 응집되어야하는지 알 수 있다.

설정 메타데이터는 XML,Java annotations,자바 코드로 표현된다.

이를 통해 애플리케이션을 구성하는 객체와 이러한 객체간의 상호 의존성을 표현할 수 있다.

ApplicationContext 인터페이스는 몇몇 구현 방법들을 지원한다.

싱글톤 어플리케이션에서는 ClassPathXmlApplicationContext또는 FileSystemXmlApplicationContext의 인스턴스를 만드는것이 일반적이다.

XML은 메타데이터를 정의하는 전통적인 방식이지만

자바 어노테이션이나 코드를 통해 메타데이터를 정의할 수 있다.

어플리케이션은 ApplicationContext가 생성되고 초기화되었을때 설정 메타데이터와 병합하게 된다.

그러면 완전히 구성되고 실행가능한 시스템(어플리케이션)이 된다.

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

### 1.2. Container Overview

org.springframework.context.ApplicationContext 인터페이스는 스프링 IoC컨테이너를 의미하고

빈들을 생성하고 설정하고 응집시킬 책임이 있다.

컨테이너는 설정 메타데이터를 읽어서 어떤 객체가 생성되고 설정되고 응집되어야하는지 알 수 있다.

설정 메타데이터는 XML,Java annotations,자바 코드로 표현된다.

이를 통해 애플리케이션을 구성하는 객체와 이러한 객체간의 상호 의존성을 표현할 수 있다.

ApplicationContext 인터페이스는 몇몇 구현 방법들을 지원한다.

싱글톤 어플리케이션에서는 ClassPathXmlApplicationContext또는 FileSystemXmlApplicationContext의 인스턴스를 만드는것이 일반적이다.

XML은 메타데이터를 정의하는 전통적인 방식이지만

자바 어노테이션이나 코드를 통해 메타데이터를 정의할 수 있다.

어플리케이션은 ApplicationContext가 생성되고 초기화되었을때 설정 메타데이터와 병합하게 된다.

그러면 완전히 구성되고 실행가능한 시스템(어플리케이션)이 된다.

![](이미지/img.png)

### 1.2.1. Configuration Metadata

위의 그림에서 보여주듯이 스프링 IoC컨테이너는 설정 메타데이터를 사용한다

설정 메타데이터는  Spring 컨테이너 애플리케이션의 객체를 인스턴스화, 구성하는 방법을 나타냅니다.

설정 메타데이터는 XML 포맷으로 전통적으로 간단하고 직관적으로 지원하고 Spring IoC컨테이너의 주요 개념과 기능을 이번장에서 전달하기위해 사용한다.

- 어노테이션 방식 설정 : 스프링 2.5 버전부터 지원
- 자바기반 설정 : @Configuration, @Bean, @Import, @DependsOn 스프링 3.0부터 도입되었다.

스프링 설정은 컨테이너가 관리해야하는 하나 이상의 빈의 정의로 이루어진다.

XML은 <beans/>요소 내부의  <bean/> 으로 이루어진다.

자바는 @Configuration클래스내부의 @Bean어노테이션으로 사용한다.

이러한 빈 정의는 애플리케이션을 구성하는 실제 객체에 해당한다.
