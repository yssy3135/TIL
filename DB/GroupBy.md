## Group By 처리
- Group By 는 쿼리가 스트리밍 처리를 할 수 없게 하는 처리중 하나
- Having은 필터링 역할 Group By는 인덱스를 사용해 처리될 수 없어 HAVING 절을 튜닝하려고 인덱스를 생성하거나 다른 방법을 고민할 필요는 없다.


**Group By 작업도 인덱스를 사용하는 경우와 그렇지 못한 경우로 나눌 수 있다.**
- 인덱스를 사용할 경우 차례대로 스캔하는 인덱스 스캔 방법과, 인덱스를 건너 뛰면서 읽는 루스 인덱스 스캔 방법으로 나눔


### 인덱스 스캔을 이용하는 GROUP BY(타이트 인덱스 스캔)
- Order By 와 마찬가지로 조인의 드라이빙 테이블에 속한 컬럼만을 이용해 그루핑 할 때 Group By 컬럼에 이미 인덱스가 있다면 인덱스를 차례대로 읽으면서 그루핑 작업을 수행하고 그 결과로 조인을 처리
-  Group By가 인덱스를 사용해서 그룹핑을 한다고 해도 임시 테이블이 필요한 경우가 있음.
    - 그룹 함수(Aggregation function) 등의 그룹값을 처리해야 할 때
- 인덱스를 사용해서 처리되는 Group By는 이미 정렬된 인덱스를 읽는 것이기 때문에 실행시점에 추가적인 정렬이나 임시 테이블 사용이 필요하지 않다.


### 루스 인덱스 스캔을 이용하는 GROUP BY
- 루스 인덱스 스캔 방식은 레코드를 건너 뛰면서 필요한 부분만 읽어 가져오는 방식
- 옵티마이저가 루스 인덱스 스캔을 사용하면 실행계획에 "Using index for group-by" 코멘트가 표시


```
mysql> EXPLAIN
		SELECT emp_no
		FROM salaries
		WHERE from_date = '1985-03-01'
		GROUP BY emp_no;
```

![Pasted image 20230927151702.png](image%2Fgroup%20by%20image%2FPasted%20image%2020230927151702.png)

- (emp_no, from_date)로 인덱스가 생성되어 있음.
- 위 쿼리의 where 조건은 인덱스 레이지 스캔 방식으로 접근할 수 없는 쿼리.
- 하지만 실행계획을 봤을때 range 쿼리를 사용, Group By 처리까지 인덱스를 사용했다

**쿼리를 어떻게 실행했을까?**
1. (emp_no, from_date) 인덱스를 차례대로 스캔하면서 emp_no의 첫 번째 유일한 값(그룹 키) "10001" 찾아냄
2. (emp_no, from_date) 인덱스에서 emp_no가 '10001' 인 것 중에서 from_date 값이 '1985-03-01' 인 레코드만 가져옴.
    - 이 방법은 1단계에서 알아낸 '10001' 값과 쿼리의 WHERE 절에 사용된 'from_date = '1985-03-01' 조건을 합쳐 "emp_no=10001 AND from_date = 1985_03_01 조건으로 (emp+no, from_date) 인덱스를 검색하는 것과 거의 흡사
3. (emp_no, from_date) 인덱스에서 emp_no의 그다음 유니크한(그룹 키) 값을 가져온다.
4. 3번 단계에서 결과가 더 없으면 처리 종료, 결과가 있다면 2번 과정으로 돌아가서 반복 수행


**루스 인덱스 스캔에 다시 한번 기억하자**
- 루스 인덱스 스캔 방식은 단일 테이블에 대해 수행되는 Group By 처리에만 사용할 수 없다.
- 프리픽스 인덱스(컬럼값의 앞쪽 일부만으로 생성되는 인덱스) 는 루스 인덱스 스캔을 사용할 수 없다.
- 인덱스 레인지 스캔에서는 유니크 레코드 값이 많을 수록 성능이 향상되는 반면 루스 인덱스 스캔은 유니크 레코드 값이 적을수록 성능이 향상된다.
- **루스 인덱스 스캔은 분포도가 좋지 않은 인덱스일수록 더 빠른 결과를 만들어낸다.**
- 루스 인덱스 스캔으로 처리되는 쿼리에서는 별도의 임시 테이블이 필요하지 않다.


### 임시 테이블을 사용하는 GROUP BY

**언제 임시 테이블을 사용?**
Group By의 기준이 되는 컬럼이 드라이빙 테이블에 있던 드리븐 테이블에 있든 관계없이 인덱스를 전혀 사용하지 못할 때



```
mysql> EXPLAIN
		SELECT e.last_name, AVG(s.salary)
		FROM employees e, salaries s
		WHERE s.emp_no=e.emp_no
		GROUP BY e.last_name;
```

![Pasted image 20230927160217.png](image%2Fgroup%20by%20image%2FPasted%20image%2020230927160217.png)

**"Using temporary" 만표기되고 "Using filesort" 는 표시되지 않았다 왜?**
- MySQL 8.0 이전에는 Order By를 명시적으로 사용하지 않아도 그루핑되는 컬럼을 기준으로 묵시적인 정렬까지 함께 수행됨.
- MySQL 8.0 이후 버전부터는 묵시적인 정렬은 더 이상 실행되지 않음.
- GROUP BY가 필요한 경우 **내부적으로 GROUP BY절의 컬럼들로 구성된 유니크 인덱스를 가진 임시 테이블 생성해 중복 제거와 집합 함수 연산을 수행**
- 그 이후 조인의 결과를 가져와 한건씩 중복 체크를 하면서 INSERT, UPDATE 수행 -> 별도의 정렬 작업 없이 GROUP BY가 처리