# Propagation 테스트 및 결과

로그를 통해 JPA트랜잭션의 propagation을 테스트 해보았다.

![img1.png](propagation-images%2Fimg1.png)

## 1. Required

- Required는 지정하지 않았을시 default인 속성이다

| 트랜잭션 존재 | 트랜잭션 존재하지 않음 |
| --- | --- |
| 트랜잭션 그대로 이용한다. | 새로운 트랜잭션을 생성한다. |

************parent************

![img.png](propagation-images%2Fimg.png)
**child**

![img_1.png](propagation-images%2Fimg_1.png)
******Result******

![img_2.png](propagation-images%2Fimg_2.png)
위 결과log를 보면 부모 트랜잭션이 존재한다

때문에트랜잭션 이름이 updateParent로 동일한 것을 확인할 수 있다.

**부모 트랜잭션 존재하지 않을경우 Parent 에서 updateParentNoTx()를 호출할 경우이다.**

![img_3.png](propagation-images%2Fimg_3.png)
부모 트랜잭션이 존재하지 않을 경우 새로운 트랜잭션을 생성한다.

## 2. SUPPORTS

- SUPPORTS는 현재 활성화된 트랜잭션이 있는지 확인한다.

| 트랜잭션 존재 | 트랜잭션 존재하지 않음 |
| --- | --- |
| 트랜잭션 그대로 이용한다. | 트랜잭션 없이 수행한다. |

************Parent************


![img_4.png](propagation-images%2Fimg_4.png)


**********Child**********



![img_5.png](propagation-images%2Fimg_5.png)

**result**

**************************************************기존 트랜잭션 존재**************************************************
![img_6.png](propagation-images%2Fimg_6.png)

트랜잭션이 존재하면 기존 트랜잭션을 그대로 사용하는 것을 확인할 수 있다.

***************************************************기존 트랜잭션 미존재***************************************************

![img_7.png](propagation-images%2Fimg_7.png)
트랜잭션 name 이 null이 아니기 때문에 트랜잭션이 적용되다고 생각할 수 있지만 트랜잭션이 생성이 되었지만

tx active는 false로 트랜잭션만 생성되고 적용은 되지 않았다.

**기존 트랜잭션이 존재하지 않으면 신규 트랜잭션만 생성되고 적용은 되지 않는다**

## 3. MANDATORY

- 트랜잭션이 필수적으로 필요한 propagation

| 트랜잭션 존재 | 트랜잭션 존재하지 않음 |
| --- | --- |
|  트랜잭션 그대로 이용한다. | exception이 발생한다. |

************Parent************

![img_8.png](propagation-images%2Fimg_8.png)

**********Child**********

![img_9.png](propagation-images%2Fimg_9.png)

**result**

**************************************************기존 트랜잭션 존재**************************************************

![img_10.png](propagation-images%2Fimg_10.png)
트랜잭션이 존재하면 기존 트랜잭션을 그대로 사용하는 것을 확인할 수 있다.

***************************************************기존 트랜잭션 미존재***************************************************
![img_11.png](propagation-images%2Fimg_11.png)

![img_12.png](propagation-images%2Fimg_12.png)

위 테스트를 실행시켰을때 성공하는 것을 확인할 수 있다.

따라서 부모 트랜잭션이 존재하지 않을 경우 IllegalTransactionStateException 을 발생시킨다.

## 4. NEVER

| 트랜잭션 존재 | 트랜잭션 존재하지 않음 |
| --- | --- |
|  트랜잭션 존재시 exception을 발생한다.. | 정상적으로 수행된다( 트랜잭션 없이 수행된다.) |

************Parent************
![img_13.png](propagation-images%2Fimg_13.png)

**********Child**********

![img_14.png](propagation-images%2Fimg_14.png)
**Result**

**************************************************기존 트랜잭션 존재**************************************************
![img_15.png](propagation-images%2Fimg_15.png)
![img_16.png](propagation-images%2Fimg_16.png)
테스트를 실행한 결과 성공한 것을 확인할 수 있다.

기존의 트랜잭션이 존재할 경우 IllegalTransactionStateException을 발생시킨다는 것을 알 수 있다.

***************************************************기존 트랜잭션 미존재***************************************************
![img_17.png](propagation-images%2Fimg_17.png)

트랜잭션 name 이 null이 아니기 때문에 트랜잭션이 적용되다고 생각 할 수 있다.

트랜잭션이 생성이 되었지만 tx active는 false로 트랜잭션만 생성되고 적용은 되지 않았다.

***************************************************기존 트랜잭션이 존재하지 않을 경우 새 트랜잭션은 생성하지만 적용하지 않는다***************************************************

## 5. NOT_SUPPORTED

| 트랜잭션 존재 | 트랜잭션 존재하지 않음 |
| --- | --- |
| 기존 트랜잭션을 적용한 부분에만 트랜잭션이 적용. | 트랜잭션 없이 실행된다. |

************Parent************

![img_18.png](propagation-images%2Fimg_18.png)
**********Child**********

![img_19.png](propagation-images%2Fimg_19.png)
**Result**

**************************************************기존 트랜잭션 존재**************************************************
![img_20.png](propagation-images%2Fimg_20.png)

- 결과는 부모 트랜잭션을 사용하지 않고 새로운 트랜잭션이 만들어졌지만 적용되지 않았다
- 어떤 의미인지 잘 이해가 가지 않아 Exception과 추가 파라미터를 넣어 다시 테스트 하였다.
  ![img_21.png](propagation-images%2Fimg_21.png)

![img_22.png](propagation-images%2Fimg_22.png)
- child 끝부분에 Exception을 넣었다.
- parent에는 먼저 name : “parent”와 age :“1” 을 넣고 저장했다.
- 그리고 child 에서  name : “child”로 다시 저장한다 그리고 Exception이 발생
- 그후 parent에서 “not support”로 다시 저장한다.
- 그리고 데이터를 확인했다.

![img_23.png](propagation-images%2Fimg_23.png)
- 결과는 child parent에서 name : “parent”로 저장했지만
- child에서는 exception이 발생 transaction이 적용되지 않아 name : “child” 그대로 남아있다.
- 하지만 child 에는 age를 변경하지 않았지만 parent 트랜잭션이 적용되어 rollback 되었다.

***************************************************기존 트랜잭션 미존재***************************************************
![img_24.png](propagation-images%2Fimg_24.png)

트랜잭션을 생성하지만 적용하지 않는다!

**NOT SUPPORTED는 기존 트랜잭션이 존재하면  NOT_SUPPORED를 적용한 부분에는 트랜잭션을 적용하지 않는다. 하지만 기존 부분에는 그대로 적용된다.**

**트랜잭션이 존재하지 않을경우 트랜잭션 없이 수행한다.**

**기존 부분에 영향을 주지 않는다!!**

## 6. REQUIRES_NEW

| 트랜잭션 존재 | 트랜잭션 존재하지 않음 |
| --- | --- |
| 새로운 트랜잭션을 생성하고 수행한다. | 새로운 트랜잭션을 생성하고 수행한다. |

************Parent************
![img_25.png](propagation-images%2Fimg_25.png)

**********Child**********
![img_26.png](propagation-images%2Fimg_26.png)

**Result**

**************************************************기존 트랜잭션 존재**************************************************
![img_27.png](propagation-images%2Fimg_27.png)

새로운 트랜잭션을 생성하고 적용한다.

********************************************************기존 트랜잭션 미존재********************************************************

![img_28.png](propagation-images%2Fimg_28.png)
기존의 트랜잭션이 존재하지 않아도 새로운 트랜잭션을 생성하고 적용하는 것을 확인 할 수 있다.

## 7. NESTED
![img_29.png](propagation-images%2Fimg_29.png)
![img_30.png](propagation-images%2Fimg_30.png)

테스트시 `NestedTransactionNotSupportedException.class` 오류가 발생한다.

DBMS 특성에 따라 지원 여부가 달라진다.

현재  'mysql:mysql-connector-java:8.0.32’ implementation 하여 사용중이 였지만 지원하지 않았다.

**기존** **트랜잭션 존재**

- 기존 트랜잭션에 Save Point를 걸고 이후 트랜잭션을 수행
- Exception 발생시 SavePoin까지 롤백되며, Save Point 이후는 트랜잭션 처리

**트랜잭션 미존재**

- 새로운 트랜잭션을 생성하고 로직수행

## 정리

| 종류 | 트랜잭션존재 | 트랜잭션 미존재 | 비고 |
| --- | --- | --- | --- |
| REQUIRED | 기존 트랜잭션 이용 | 신규 트랜잭션 생성 | 기본설정이다 |
| SUPPORTS | 기존 트랜잭션 이용 | 트랜잭션 없이 수행 |  |
| MANDATORY | 기존 트랜잭션 이용 | Exception 발생 | 꼭 이전트랜잭션이 있어야 하는경우 |
| NEVER | exception이 발생한다 | 트랜잭션 없이 수행 | 트랜잭션 없을때만 작업이 진행되어야할때 |
| NOT_SUPPORTED | 기존 트랜잭션을 적용한 부분에만 트랜잭션이 적용. | 트랜잭션 없이 수행 | 기존 트랜잭션에 영향을 주지 않아야할때 사용. |
| REQUIRES_NEW | 새로운 트랜잭션을 생성하고 수행한다. | 새로운 트랜잭션을 생성하고 수행한다. | 이전트래잭션과 구분하여 새로운 트랜잭션으로만 처리가 필요할때 사용 |
| NESTED | 현재 트랜잭션에 Save Point를 걸고 이후 트랜잭션을 수행 |  신규 트랜잭션을 생성하고 로직이 수행 | DBMS특성에 따라 지원 혹은 미지원 |