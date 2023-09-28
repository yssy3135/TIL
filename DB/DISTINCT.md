## DISTINCT 처리

**집합 함수와 함께 사용하는 경우와 집합 함수가 없는 경우**
- DISTINCT 키워드가 영향을 미치는 범위가 달라지기 때문
- DISTINCT 처리가 인덱스를 사용하지 못할때는 항상 임시 테이블이 필요하다.
  하지만 실행계획의 Extra 컬럼에는 Using temporary 메시지가 출력되지 않는다.


### SELECT DISTINCT
- SELECT 되는 레코드 중에서 유니크한 레코드만 가져오고자 하면 SELECT DISTINCT 형태의 쿼리 문장을 사용한다.
    - 이 경우는 GROUP BY와 동일한 방식으로 처리됨.
    - MySQL 8.0 부터 ORDER BY를 명시하지 않으면 정렬을 사용하지 않기 때문에 두 쿼리는 내부적으로 같은 작업을 수행

**자주 실수하는 경우**
- DISTINCT는 SELECT 하는 레코드(튜플) 을 유니크하게 SELECT 하는 것이지, 특정 컬럼만 유니크하게 조회하는 것이 아님.
- SELECT 절에 사용된 DISTINCT 키워드는 조회되는 모든 컬럼에 영향을 미침.
    - 여러 컬럼중 일부 컬럼만 유니크하게 조회하는 것은 아님.
    - 집합 함수와 함께 사용된 DISTINCT의 경우는 조금 다르다.


### 집합 함수와 함께 사용된 DISTINCT
- COUNT(), MIN(), MAX 같은 집합 함수 내에서 DISTINCT 키워드가 사용될 수 있음.

**집합 함수가 있는 SELECT 와 집합 함수가 없는 SELECT **

집합 함수가 없는 SELECT 쿼리의 DISTINCT
- DISTINCT 조회 하는 모든 컬럼의 조합이 유니크 한 것들만 가져옴

집합 함수 내에서 사용된 DISTINCT
- 집합 함수로 전달된 컬럼값이 유니크한 것들을 가져옴.


**케이스 별로 알아보자**

```
1.
SELECT DISTINCT first_name, last_name
FROM employees
WHERE empno_ BETWEEN 10001 AND 10200;

2.
SELECT COUNT(DSITINCT first_name), COUNT(DISTINCT last_name)
FROM emplyees
WHERE emp_no BETWEEN 10001 AND 10200;

3.
SELECT COUNT(DISTINCT first_name, last_name)
FROM employees
WHERE emp_no BETWEEN 10001 AND 10200;

```

**1.번 DISTINCT 문**
- firstname 과 lastname 두개 컬럼의 조합이 유니크 한 것들을 가져옴

**2.번 집합함수 + DISTINCT**
- first_name이 유니크한 컬럼의 COUNT +  last_name이 유니크한 컬럼 COUNT

**3.번 집합함수 + DISTINCT 여러컬럼**
- first_name, last_name 조합이 유니크한 컬럼의 COUNT
