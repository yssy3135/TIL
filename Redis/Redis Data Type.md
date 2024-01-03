# Strings

- 대표 기본 타입 바이너리, 문자 데이터를 저장
    - Maximum 512MB
    - 객체를 직렬화한 Serializable object 를 다루기 좋다
- 증가 감소에 대한 원자적 연산
    - increment/decrement

### command

- SET
    - O(N)
- SETNX
    - 값이 없는경우에만 저장
    - SET 대체 가능
- GET
    - O(N)
- MGET
    - 여러개의 키값 한번에 가져올수 있다
    - 여러번의 요청이 필요할 경우 MGET을 활용할시 성능상 이득을 얻을수 있다
- INC
    - INCR  → 1증가
    - INCRBY abc 10 → abc 라는 키값의 value에 10증가
- DEC

# Lists

- LinkedList(strings)
- push, pop 연산에 O(1)로 동작
- Queue, Stack으로 활용

### command

- LPUSH
    - Left push
- RPUSH
    - Right push
- LPOP
    - Left pop
- RPOP
    - Right pop
- LLEN
    - List 길이 확인
- LRANGE
    - List 인덱스 값 확인

# Sets

- 순서가 없는 collection
- Java set과 동일
- 유니크한 값 보장
    - SNS follow
    - BlackList
    - Tags

### command

- SADD
    - O(1)
    - 값 추가
- SREM
    - O(1)
    - 값 삭제
- SISMEMBER
    - O(1)
    - 특정 값 포함되어 있는지 확인
- SMEMBERS
    - O(N)
    - set 전체 데이터 조회
- SINTER
    - O(M*N)
    - 두개 이상의 set에서 공통 데이터 추출
- SCARD
    - O(1)
    - set에 포함된 데이터 개수 조회

# Sorted Set

- set 기반
    - 특정 점수 기반으로 정렬 가능

### command

- ZADD
    - Olog(N)
    - 추가
- ZREM
    - Olog(N)
    - 삭제
- ZRANGE (6.2 버전 부터 전체 집합, 오름차순, 내림차순 , 순위에 따라 가져올 수 있다.)
    - 집합 요소 조회
- ZCARD
    - 집합 개수 조회
- ZRANK/ZREVERANK
    - 오름차순/ 내림차순 순위
- ZINCRBY
    - 점수 증가

# Hashes

- Field-value 쌍으로 저장할 수 있는 collection
- Java HashMap

### command

- HSET
    - O(1)
    - 값 설정
- HGET,HMGET
    - O(1)
    - 값 조회, 여러개
- HGETALL
    - 속해있는  Field-value  전체 조회
- HDEL
    - O(1)
    - 특정 필드 삭제
- HINCRBY
    - O(1)
    - 특정 필드 숫자값 증가

# Geospatial

- 위, 경도 정보 저장
- 특정 위, 경도 반경 몇km 안에 포함되어 있는지 계산에 활용
- 위치 기반 서비스, 지리 정보 광고 타켓팅 등 활용

### **command**

- GEOADD
    - Olog(N)
    - 추가
- GEOSEARCH
    - O(N+M)
    - 위 경도 기반 조회
- GEODIST
    - 저장된 위치간 거리 계산
- GEOPOS
    - key에 대한 위, 경도 조회

# Bitmap

- 0또는 1의 갑승로 이루어진 비트열
- 메모리를 적게 사용하여 대량의 데이터 저장에 유용

### command

- SETBIT
    - O(1)
    - 설정
- GETBIT
    - O(1)
    - 조회
- BITCOUNT
    - O(N)
    - 1로 설정된 bit count