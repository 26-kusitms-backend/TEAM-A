# Trasaction

### Transaction 정의

- 데이터베이스의 상태를 변경시키기 위해 수행하는 작업 단위. (또는 쪼갤 수 없는 업무 처리의 최소 단위)

<br/>

### 접근 기술 별 Transaction  코드

```java
JDBC

public void accountTransfer(String fromId, String toId, int money) throws
SQLException {
   Connection con = dataSource.getConnection();
   
   try {
    con.setAutoCommit(false); //트랜잭션 시작
    //비즈니스 로직
    bizLogic(con, fromId, toId, money);
    con.commit(); //성공시 커밋
   } catch (Exception e) {
    con.rollback(); //실패시 롤백
    throw new IllegalStateException(e);
   } finally {
    release(con);
   }
}
```


```java
JPA

public static void main(String[] args) {
  //엔티티 매니저 팩토리 생성
  EntityManagerFactory emf =
    Persistence.createEntityManagerFactory("jpabook");
  EntityManager em = emf.createEntityManager(); //엔티티 매니저 생성
  EntityTransaction tx = em.getTransaction(); //트랜잭션 기능 획득
  
  try {
    tx.begin(); //트랜잭션 시작
    logic(em); //비즈니스 로직
    tx.commit();//트랜잭션 커밋
  } catch (Exception e) {
    tx.rollback(); //트랜잭션 롤백
  } finally {
    em.close(); //엔티티 매니저 종료
  }
  emf.close(); //엔티티 매니저 팩토리 종료
}
```

<br/>

- 트랜잭션을 사용하는 코드는 데이터 접근 기술마다 다르다. 
JDBC 트랜잭션에 의존하다가 JPA 기술로 변경하게 되면 서비스 계층의 트랜잭션을 처리하는 코드도 모두 함께 변경해야 한다.

> 이 문제를 해결하기 위해 트랜잭션 기능을 추상화하면 된다.

<br/>

![image](https://user-images.githubusercontent.com/92728780/194052907-9194fc89-703c-4860-b773-ae134192f378.png)

출처: 인프런 김영한님 강의 - 스프링 DB 1편
