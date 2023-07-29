# GC란?

### GC 용어 정리

1. Stop The World
    - GC 수행을 위해 JVM이 애플리케이션 실행을 일시 정지하는것.
    - GC가 수행되면 GC 작업을 맞틍ㄴ 스레드를 제외한 나머지 스레드는 모두 멈추게되고 GC 작업이 종료되면 재개
2. Mark
    - 애플리케이션이 일시 중지되면 GC는 참조되고 있는 객체와 연결된 객체를 타고 이동하며 접근 가능한 객체를 식별하는 과정
3. Sweep
    - 모든 객체 탐색이 끝나면 식별(Mark) 되지 않는 객체들을 메모리에서 해제시키는 과정
4. Young
    - 새롭게 생성된 객체가 할당(Allocation)되는 영역
    - 대부분의 객체가 금방 Unreachable(접근 불가) 상태가 되기 대문에, 대부분의 객체가 Young 영역에서 생성되었다가 사라짐
    - Young 영역에 대한 가비지 컬렉션을 MinorGC 라고 부름

> Young 영역중 Eden 영역이 꽉 차게되면 발생
>

1. Old
    - Young 영역에서 Reachable 상태를 유지하여 살아남은 객체가 복사되는 영역
    - Old 영역은 Young 영역보다 크게 할당되며 큰 만큼 가비지는 적게 발생
    - 가득 차게 되면 Major GC가 발생

1. Eden
    - 새로 생성된 객체가 할당되는 영역
    - Eden 영역이 꽉차면 GC가 발생하면서 Mark(참조 여부 식별), Sweep(메모리 해제) 과정이 일어난다.
    - 아직 사용중인 객체는 Survivor 영역으로 이동하며, Eden 영역은 비워진다.

1. Survivor
    - Eden 영역이 꽉 차게 되면, GC가 발생하면서 제거된 객체외의 나머지 살아남은 객체는 다른 Survivor영역으로 이동하게 된다.
    - Survivor 두 영역중 하나는 반드시 비어있는 상태.
    - 두 영역 모두 데이터가 존재하거나, 사용량이 0이라면 정상적인 상황이 아니다.

******************Promotion******************

- MinorGC가 일어나 살아남은 객체의 age가 증가할때 일정 age 이상이되면 Old 영역으로 이동하게됨

![img.png](gc_memory.png)

**Survivor 영역은 왜 두개일까?**

- 메모리의 외부 단편화 발생을 방지
- 메모리가 할당되고 해제되기를 반복하다보면 메로리공간은 남지만 파편화 되어있어 메모리를 할당할 수 없는 문제가 발생
- 그래서 두개의 Survivor끼리 번갈아가며 메모리를 할당하며 이를 방지

**Minor GC**

1. Eden 영역과 survivor영역1에 있는 객체들을 Mark,Sweep 과정을 거쳐 살아남은 객체를 survivor2에 복사
2. survivor1을 Clear
3. 살아남은 객체들은 Survivor2에 남게된다.
4. 다음번 MinorGC가 발생하면 같은 방식으로 Eden 영역과 survivor2영역에 살아있는 객체를 Mark,Sweep 과정을 거쳐 survivor1에 복사, 결과적으로 survivor1에만 살아있는 객체가 남게됨.
5. 이렇게 Minor GC가 발생할 경우 살아남은 객체를 age가 증가.
6. 특정 age가 일정 이상이 되면 Old 영역으로 이동하게 된다. → Promotion
7. Promotion의 기준이 되는 Age는 `XX:MaxTenuringThreshold` 옵션으로 설정할 수 있다.
    - Java SE 8 에서의 default 값은 15 이다. 설정 가능한 범위는 0 ~ 15.


**********Major GC**********

- old 영역이 가득차면 발생
- Minor GC 과정에서 삭제되지 않고, Old Generation 영역으로 옮겨진 객체 중 미사용된다고 판단되는 객체를 삭제하는 GC
- 대표적으로 mark & Sweep 알고리즘 사용

**************Mark & Sweep 알고리즘**************

1. GC root로부터 모든 객체들의 참조를 확인하면서 참조가 연결되지 않은 객체를 Mark 한다.
2. 1.의 작업이 끝나면 사용되지 않는 객체를 모두 표시하고 이 표시된 객체를 Sweep 한다.

**GC root란?**

Runtime Data Area에서 Method Area, Native Stack (JNI), Java Stack 등에서 Heap 메모리의 Object들을 참조하는 데이터 영역

![img.png](run_time_data_area.png)

**Full GC**

- Heap 메모리 전체 영역에서 발생
- Old, Young 영역 모두에서 발생
- Minor GC, Major GC모두 실패했거나, Young 영역과 Old 영역 모두 가득 찼을때 발생
- 대표적으로 Mark & Sweep & Compact 알고리즘 사용

**Mark & Sweep & Compact**

1. 전체 객체들의 참조를 확인하면서 참조가 연결되지 않은 객체를 Mark 한다.
2. 1.의 작업이 끝나면 사용되지 않는 객체를 모두 표시하고 표시된 객체를 Sweep 한다.
3. 메모리를 정리하여 단편환를 해결할 수 있도록 한다.

**************MinorGC, MajorGC로 구분되는 이유는?**************

JVM은 Heap영역을 설계할때 2가지 존제로 설계

- 대부분 객체가 금방 접근 불가능한 상태가 된다. → 약한 세대 가설
- 오래된 객체에서 새로운 객체로의 참조는 드물게 존재한다.

객체는 일회성인 경우가많고, 메모리에 오래 남아있는 경우가 드물기때문에 young, old 영역으로 분리하여 설계

따라서 자주 발생하는 Minor GC, 비교적 적게 발생하는 Major GC로 나눠져 발생