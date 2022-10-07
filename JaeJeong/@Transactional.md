# 📍 @Transactional 어노테이션

## @Transactional 옵션

### isolation
격리 수준, 트랜잭션에서 일관성없는 데이터 허용 수준을 결정
```java
@Transactional(isolation = Isolation.XXX)
public void transaction(String test) {
  ...
}
```
- DEFAULT : DBMS의 기본 격리 수준 적용
- READ_UNCOMMITED (level 0) : 트랜잭션의 동시 액세스 허용
- READ_COMMITED (level 1)
  - Dirty read 방지
  - Postgres, SQL Server 및 Oracle의 기본 수준
- REPEATEABLE_READ (level 2)
  - 동시 액세스 허용 X
  - 업데이트 손실을 방지하기 위해 필요한 가장 낮은 수준
  - 동시 액세스 허용 X
  - MySQL의 기본 수준
- SERIALIZABLE (level 3)
  - 가장 높은 격리 수준 → 모든 부작용 방지
  - 그러나 동시 호출을 순차적으로 실행하기 떄문에 성능 저하의 우려가 있다.

<br>

### propagation
전파 옵션, 동작 도중 다른 트랜잭션을 호출할 떄 어떻게 할 것인지 지정
```java
@Transactional(propagation = Propagation.XXX)
public void transaction(String test) {
  ...
}
- REQUIRED (default) : 활성 트랜잭션이 있는지 확인하고, 없으면 새 트랜잭션을 생성
- SUPPORTS : 활성 트랜잭션이 있으면 사용하고, 없으면 트랜잭션 없이 실행
- MANDATORY : 활성 트랜잭션이 있으면 사용하고, 없으면 예외 발생 (독립적으로 트랜잭션을 진행하면 안되는 경우에 사용)
- NEVER : 활성 트랜잭션이 있으면 예외 발생 (트랜잭션을 사용하지 않도록 제어할 경우)
- NOT_SUPPORTED : 활성 트랜잭션이 있으면 트랜잭션을 일시 중단한 다음, 트랜잭션없이 비즈니스 로직 실행
- REQUIRED_NEW : 활성 트랜잭션이 있으면, 현재 트랜잭션을 일시 중단하고 새 트랜잭션을 생성
- NESTED
  - 트랜잭션이 존재하는지 확인하고 존재하는 경우 저장점을 표시
  - 비즈니스 로직 실행에서 예외가 발생하면 트랜잭션이 이 저장 지점으로 롤백
  - 활성 트랜잭션이 없으면 REQUIRED처럼 작동
 
<br>

### noRollbackFor
특정 예외 발생 시 롤백이 동작하지 않도록 설정
```java
@Transactional(noRollbackFor = Exception.class)
public void transaction(String test) {
  ...
}
```

<br>

### RollbackFor
특정 예외 발생 시 롤백이 동작하도록 설정 
```java
@Transactional(rollbackFor = Exception.class)
public void transaction(String test) {
  ...
}
- 선언적 트랜잭션(@Transactional)에서는 RuntimeException이 발생하면 롤백 수행
- 예외가 발생하지 않거나 체크 예외 발생 시 커밋
- 스프링에서는 데이터 액세스 기술 예외는 RuntimeException으로 던져지므로, RuntimeException만 롤백 대상으로 삼는다.

<br>

### timeout
지정 시간 내에 메소드 수행이 완료되지 않으면 롤백이 동작하도록 설정
```java
@Transactional(timeout = 10)
public void transaction(String test) {
  ...
}
```
- 시간 단위 : second
- default는 -1 => -1일 경우 no timeout

<br>

### readOnly
트랜잭션을 읽기 전용으로 설정
```java
@Transactional(readOnly = true)
public void transaction(String test) {
  ...
}
```
- default는 false
- 성능 최적화를 위해 사용하거나, 특정 트랜잭션 작업 안에서 쓰기 작업이 일어나는 것을 의도적으로 방지하기 위해 사용
- readOnly = true인 경우, INSERT/UPDATE/DELETE 작업 실행 시 예외가 발생한다.

<br>
.
<br>
.


💡 테스트 환경에서의 @Transactional

=> 테스트 메소드가 종료될 때 자동으로 롤백된다 !
