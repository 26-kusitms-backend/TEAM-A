# MyBatis 

SQL문을 조작하기 전과 후의 Connection 및 객체 매핑을 대신 해주어 편의성을 높여준다.

SQL문을 코드와 분리하여 유지보수성을 높여준다.

SQL 실행 결과를 VO ( Value Object )로 매핑해준다.

## 어플리케이션 시작 시, MyBatis 빈 객체 생성 Flow

1. Spring Application은 SqlSessionFactoryBuilder에게 SqlSessionFactory 빌드를 요청한다.
2. SqlSessionFactoryBuilder는 SqlSessionFactory 생성을 위한 MyBatis 구성 파일 ( config )를 읽는다.
3. SqlSessionFactoryBuilder는 MyBatis 구성 파일을 토대로 SqlSessionFactory를 생성한다.

## 클라이언트 요청에 따른 Flow

1. 클라이언트는 DB 테이블에 특정 Row를 읽어오라고 요청한다.
2. Spring Application은 Mapper inteface 구현체의 메소드를 호출한다.
3. 메소드에 해당하는 SqlSession 메소드를 호출한다.
4. SqlSession은 트랜잭션에 할당된 SqlSession이 없으면 SqlSessionFactory 메소드를 호출한다.
5. 매핑 파일에서 실행할 SQL을 실행한다.

https://linked2ev.github.io/mybatis/2019/09/08/MyBatis-1-MyBatis-%EA%B0%9C%EB%85%90-%EB%B0%8F-%EA%B5%AC%EC%A1%B0/

https://pangtrue.tistory.com/141

예제로 살펴보기 좋은 블로그 ⬇️

https://devncj.tistory.com/48