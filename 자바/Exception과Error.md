# Exception 과 Error

# 예외란? (Error vs Exception)

**자바에서는 실행시(runtime) 발생할 수 있는 오류를 에러와 예외 두 가지로 구분한다.**

### 에러(Error)

- 메모리부족 (OutOfMemory) 나 (StackOverflow)와 같이 발생하면 복구할 수 없는 오류
- 시스템에 비정상적인 상황이 생겼을 때 발생한다.
- 시스템 레벨에서 발생하기 때문에 심각한 수준의 오류이다.
- 개발자가 미리 예측할 수 없다.
- 에러는 일반적으로 복구할 수 없으며 프로그램의 실행을 중단시킨다.

### 예외(Exception)

- 예측가능한 오류
- 런타임 예외 (Unchecked Exception) 과 예외 (Checked Exception) 으로 나눠진다.
- 런타임 예외
    - 런타임 예외는 명시적인 예외 처리가 필요하지 않고, 프로그래머의 실수에 의한 오류를 나타낸다.
        
        (인덱스 범위 초과 out of index, null 객체에 접근 nullPointer)
        
- 일반 예외
    - 일반 예외는 반드시 예외 처리를 해야한다.
        
        (파일 입출력 오류, 네트워크 연결 오류 등) 외부적인 요인에 의한 오류를 나타낼 수 있다.
        
        try-catch , throws 등을 사용하여 처리한다.
        
    - 예외가 발생해도 트랜잭션에 의해 롤백되지 않는다(스프링)

|                 | checked exception                                  | unchecked exception     |
|-----------------|----------------------------------------------------|-------------------------|
| 처리 여부           | 반드시 예외 처리                                          | 명시적인 처리를 강제하지 않음        |
| 확인 시점           | 컴파일 단계                                             | 실행단계                    |
| 예외 발생 시 트랜잭션 처리 | roll-back 하지 않음                                    | roll-back 함             |
| 대표 예외           | Exception의 상속받는 하위 클래스중RuntimeException을 제외한 모든 예외 | RuntimeException의 하위 예외 |

***************************************************예외처리의 정의와 목적***************************************************

프로그램의 실행도중에 발생하는 에러는 어쩔 수 없지만, 예외는 개발자가 이에 대한 처리를 미리 해야한다.

프로그램 실행 시 발생할 수 있는 예기치 못한 예외의 발생에 대비한 코드를 작성하는 것이며, 

예외처리의 목적은 예외의 발생으로 인한 실행 중인 프로그램의 갑작스런 비정상 종료를 막고,

 정상적인 실행상태를 유지할 수 있도록 하는것.

**정리**

예외처리(exception handling)은 프로그램 실행시 발생할 수 있는 예외의 발생에 대한 코드를 작성하는 것.

예외처리의 목적은 프로그램의 비정상 종료를 막고, 정상적인 실행상태를 유지하는 것.

자바에서는 실행 시 발생할 수있는 오류 

자바에서 예외 클래스는 두 그룹으로 나눌 수 있다.

- Exception 클래스와 자손 (Runtime Exception 제외)
- Runtime Exception 클래스와 그 자손

1. java.lang.Throwable:
    - 모든 예외 클래스의 최상위 클래스.
    - 예외와 에러의 공통 기능을 제공.
2. java.lang.Error:
    - 시스템 수준의 심각한 오류를 나타냄.
    - 프로그램이 복구할 수 없는 상태가 되었을때 발생.
    - 주로 가용 자원의 부족, 가상 머신 동작 오류 등을 포함합니다.
3. java.lang.Exception:
    - 예외 상황을 나타내는 클래스들의 상위 클래스.
    - 일반적인 예외와 런타임 예외로 구분.
4. java.lang.RuntimeException:
    - 런타임 예외를 나타내는 클래스들의 상위 클래스.
    - 주로 프로그래머의 실수나 잘못된 사용으로 인한 예외.
    - 예외 처리를 강제하지 않으며, 명시적인 예외 처리가 선택 사항입니다.
5. 일반 예외 클래스:
    - RuntimeException을 제외한 모든 예외 클래스는 일반 예외로 분류됩니다.
    - 예외 처리가 필요하며, 반드시 예외 처리를 해야 합니다.
    - IOException, SQLException, ClassNotFoundException 등이 일반 예외 클래스의 예시.

![img_1.png](img_1.png)