

| index datastructure | B-Tree                                                                                                                                                      | Hash                                                  |
|:--------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------|
| = 조회                | Average: O(logN)<br/>Worst: O(logN)| Average: O(1)<br/>Worst: O(N)                         |
| <, > range 조회       | O(logN + M) | 사용 불가                                                 |    
| 데이터 삽입              | Average: O(logN)<br/>Worst: O(logN) | Average: O(1) <br/>Worst: O(N) |
| 데이터 삭제              | Average: O(logN)<br/>Worst: O(logN) | Average: O(1) <br/>Worst: O(N) |
## B트리 Index

range query가 빈번할 경우 매우 유리하다

range query에 사용되는 데이터의 시작점과 끝점을 찾으면

그 사이에 해당하는 데이터들을 tree traversal(중위순회) 알고리즘으로 바로 가져올 수 있기 때문

## Hash Index

Hash index를 생성시 equality 쿼리의 경우는 매우 빠른시간안에 찾을 수 있다

range query의 경우 hash index는 사용되지 않는다.

해시버킷에 저장된 순서와 실제 row의 순서에 연관이 없기 때문이다.

## index를 생성 시 고려해야할 것

- index를 생성하면, 데이터의 생성시 추가적인 연산이 필요하다
  B-Tree의 경우 Balanced Tree에서의 모든 연산은 O(logn) 이다.
  Hash Table의 경우 모든 연산이 O(1) 이지만, 해시테이블의 상태에 따라 달라질 수 있다.

- range query가 필요하다면 B-tree index를 생성,
  equality query만 필요하다면 hash index가 유리하다