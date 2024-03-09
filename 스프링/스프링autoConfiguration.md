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

개발자가 정의한 Component들이 먼저 스캔돼서 Bean으로 등록된 이후에 AutoConfiguration의 Bean들이 등록됨.