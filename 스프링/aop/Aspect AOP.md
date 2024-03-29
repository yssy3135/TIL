

- 스프링 애플리케이션에 프록시를 적용하려면 포인트컷과 어드바이스로 구성되어 있는 어드바이저(Advisor)를 만들어서 스프링 빈으로 등록하면 자동 프록시 생성기가 모두 자동으로 처리해준다.
- 자동 프록시 생성기는 스프링 빈으로 등록된 어드바이저들을 찾고 스프링 빈들에 자동으로 프록시를 적용해준다. (포인트컷이 매칭되는 경우)

## @Aspect 프록시

- 관점 지향 프로그래밍(AOP)를 가능하게 하는 AspectJ 프로젝트에서 제공하는 애노테이션
- 스프링은 @Aspect 애노테이션으로 매우 편리하게 포인트컷과 어드바이스로 구성되어 있는 어드바이저 생성 기능을 지원한다.
- 자동 프록시 생성기가 @Aspect를 찾아서 이것을 Advisor로 만들어준다.(변환해서 저장하는 기능도 한다.)
- 그래서 이름 앞에 Annotation(애노테이션을 인식하는)가 붙어 있는것.

![img_16.png](img%2Fimg_16.png)

***************************************************자동 프록시 생성기는 2가지 일을 한다.***************************************************

1. @Aspect를 보고 어드바이저(Advisor)로 변환해서 저장한다.
2. 어드바이저를 기반으로 프록시를 생성한다.

![img_17.png](img%2Fimg_17.png)

### @Aspect를 어드바이저로 변환해서 저장하는 과정

1. 실행 : 스프링 애플리케이션 로딩 시점에 자동프록시 생성기 호출
2. 모든 @Aspect 빈 조회 : 자동 프록시 생성기는 스프링 컨테이너에서 @Aspect 애노테이션이 붙은 스프링 빈을 모두 조회한다.
3. 어드바이저 생성 : @Aspect 어드바이저 빌더를 통해 @Aspect애노테이션 정보를 기반으로 어드바이저 생성
4. @Aspect 기반 어드바이저 저장 : 생성한 어드바이저를 @Aspect 어드바이저 빌더 내부에 저장한다.

******@Aspect 어드바이저 빌더******

- BeanFactoryAspectJAdvisorsBuilder 클래스
- @Aspect 정보기반으로 로인트컷, 어드바이스, 어드바이저를 생성하고 보관하는 것을 담당
- @Aspect정보를 기반으로 어드바이저를 만들고, @Aspect 어드바이저 빌더 내부 저장소에 캐시
- 캐시에 어드바이저 이미 만들어져있는경우 캐시에 저장된 어드바이저 반환

### 어드바이저를 기반으로 프록시 생성

![img_18.png](img%2Fimg_18.png)

***************************************************자동 프록시 생성기의 작동 과정***************************************************

1. 생성 : 스프링 빈 대상이 되는 객체를 생성 (@Bean, 컴포넌트 스캔 모두 포함)
2. 전달 : 생성된 객체를 빈 저장소에 등록하기 직전에 빈 후처리기에 전달
3. Advisor 빈 조회 : 스프링 컨테이너에서 Advisor빈을 모두조회
   @Aspect Advisor 조회 : @Aspect어드바이저 빌더 내부에 저장된 Advisor를 모두 조회
4. 프록시 적용 대상 체크 : 앞서 3에 조회한 Advisor에 포함되어 있는 포인트컷을 사용해서 해당 객체가 프록시를 적용할 대상인지 아닌지 판단.
   이때 클래스정보, 해당 객체의 모든 메서드를 포인트컷에 하나하나 모두 매칭해봄, 조건이 하나라도 만족하면 프록시 적용대상이 된다.(메서드 하나라도)
5. 프록시 생성 : 프록시 적용 대상이면 프록시를 생성하고 프록시 반환해 빈으로 등록 아니라면 원본 객체를 스프링 빈으로 등록
6. 빈 등록 : 반환된 객체는 스프링 빈으로 등록

실무에서 프록시를 적용할 때는 대부분 @Aspect 방식을 사용한다.

****************************횡단 관심사****************************

특정 기능 하나에 관심이 있는 기능이아니라 애플리케이션의 여러 기능들 사이에 걸쳐서 들어가는 관심사.

프록시는 이렇게 여러곳에 걸쳐있는 횡단 관심사의 문제를 해결하는 방법

![img_19.png](img%2Fimg_19.png)