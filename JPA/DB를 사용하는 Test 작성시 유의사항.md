

### beforeEach와 afterEach를 사용할 때 주의

- beforeEach와 afterEach는 테스트하려는 메소드의 Transaction을 따라가기 때문에 실행하려는 테스트가 Transaction이 걸려있을경우 flush를 날려주고 영속성 컨텍스트를 초기화 해주어야 한다.
- Transaction 단위로 영속성컨텍스트가 유지되어 실제 쿼리와 조회 결과가 다를 수 있다.
- 또한 jpql을 사용하여 DB에 바로 접근하는 쿼리 같은 경우 세팅 데이터가 없을수 있다.
