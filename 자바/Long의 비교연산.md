Long 타입의 비교연산을 하던중 예상과 다른 결과를 발견하였다

동일성(==)을 비교할 경우 (-128~127) 까지는 정상적인 결과를 반환한다.

하지만 128이상일경우 동일성으로 비교할경우 정상적인 결과를 반환하지 못한다.

따라서 동등성(equals)로 비교해주어야 한다.

### 왜?

**-128에서 127까지의 범위의 경우 상수 풀을 캐시하여 유지한다.**

```java
private static class LongCache {
        private LongCache(){}

        static final Long cache[] = new Long[-(-128) + 127 + 1];

        static {
            for(int i = 0; i < cache.length; i++)
                cache[i] = new Long(i - 128);
        }
    }
```

따라서 valueOf 함수를 보면 범위 내의 값은 캐시한 값에서 가져오는 것을 알 수 있다.

```java
@HotSpotIntrinsicCandidate
    public static Long valueOf(long l) {
        final int offset = 128;
        if (l >= -128 && l <= 127) { // will cache
            return LongCache.cache[(int)l + offset];
        }
        return new Long(l);
    }
```