## Spring boot의 AutoConfiguration이란?

- Spring Boot의 auto-configuration은 추가한 jar파일에 따라 자동적으로 설정을 해줌
- @EnableAutoConfiguration또는 @SpringBootApplication 주석을 @Configuration 클래스중 하나에 추가해주면 된다.

**특정 클래스 Auto-configuration 비활성화**

- excludeName 속성을 사용하여 이름을 전부 적어주면 된다.

```java
import org.springframework.boot.autoconfigure.*;
import org.springframework.boot.autoconfigure.jdbc.*;
import org.springframework.context.annotation.*;

@Configuration
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
public class MyConfiguration {
}
```

@SpringBootApplication은 3가지 어노테이션을 합친 것.

- @SpringBootConfiguration
  - @Configuration과 같은 기능을 한다.
- @EnableAutoConfiguration
  - classpath에 있는 /resource/META-INF/spring.factories 중 EnableAutoConfiguration 부분에 정의된 Configuration들을 자동으로 등록한다.
  - —debug 옵션을 가지고 jar파일을 실행시키면 조건 만족여부와 함께, 어떤 Bean들이 등록되는지 확인 해 볼 수 있다.
- @ConponentScan
  - base-package가 정의되지 않으면 해당 어노테이션이 붙은 classpath 하위의 @Component 어노테이션을 스캔해서, Bean으로 등록한다.

**흐름**

1. @ComponentScan 어노페이션을 통해 개발자가 정의한 Component들이 Bean으로 먼저 등록
2. @EnableAutoConfiguration 어노테이션으로 인해 AutoConfiguration이 동작 애플리케이션 구성에 필요한 추가 Bean들을 읽어서 등록

### **정리**

정리를 해보자면, 스프링 부트 자동 구성의 큰 흐름은 아래와 같이 진행됩니다.

1. main 메서드가 실행되고 @SpringBootApplication -> @EnableAutoConfiguration 어노테이션으로 인해 자동 구성을 위한 AutoConfigurationImportSelector 클래스의 내부 메서드가 동작하게 됩니다.

2. AutoConfigurationImportSelector 클래스 내부의 getAutoConfigurationEntry() 메서드 동작 과정에서 META-INF/spring/org.springframework.autoconfigure.AutoConfiguration.imports 파일에 등록된 AutoConfiguration 클래스 정보를 읽어오고 spring.factories에서는 자동 구성에 사용될 필터 정보를 읽어옵니다.

3. spring.factories에서 읽어온 3개의 필터*(OnBeanCondition, OnClassCondition, OnWebApplicationCondition)*를 통해 AutoConfiguration.imports 파일에 등록된 AutoConfiguration 클래스를 필터링합니다.

4. 필터링을 통해 실제 적용될 AutoConfiguration 클래스들만 남게 됩니다.