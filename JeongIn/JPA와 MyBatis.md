# JPA vs MyBatis

## JPA

![j1](https://user-images.githubusercontent.com/54874529/190563778-bc92ed57-dc5c-4e7c-a239-866bdff8029a.png)


Java ORM 기술 표준으로 사용되는 `인터페이스의 모음`

→ 실제 구현된 것이 아니라 구현된 클래스와 매핑을 하기 위한 용도로 JPA를 사용하기 위해선 Hibernate, EclipseLink, DataNucleus 같은 `ORM 프레임워크`를 사용해야 한다.

![JPA 2.1 표준 명세를 구현한 3가지 구현체](https://user-images.githubusercontent.com/54874529/190563776-f28752a6-83f0-46d1-b21e-0859735b4d04.png)

JPA 2.1 표준 명세를 구현한 3가지 구현체

### JPA 구성요소

1. javax.persistance 패키지로 정의된 API
2. JPQL(Java Persistence Query Language)
3. 객체/관계 메타데이터

### Hibernate

`HQL(Hibernate Query Language)`이라 불리는 매우 강력한 쿼리 언어를 포함한다.

**HQL 특징**

1. SQL과 유사하며 추가적인 컨벤션 정의가 가능
2. 상속, 다형성, 관계 등 객체지향의 장점을 가짐
3. 자바 클래스, 프로퍼티 이름을 제외하고 대소문자 구분
4. 쿼리 결과는 객체로 반환하며 개발자가 생성, 접근 가능
5. 페이지네이션, 동적 프로파일링 등 향상된 기능 제공

**장점**

1. 생산성: SQL을 사용하지 않고 메서드 호출만으로 쿼리 수행. SQL 반복작업이 이루어지지 않으므로 생산성 향상
2. 유지보수: 테이블 컬럼 변경 시 MyBatis에서는 관련 DAO의 파라미터, 결과, SQL 등을 모두 확인해야 하나 JPA는 자동으로 이루어져 유지보수에 용이
3. 특정 벤더에 종속적이지 않음 (vendor-neutral): 추상화된 접근 계층을 제공하기 때문에 특정 벤더에 종속적이지 않아 DB 변경 용이

**단점**

1. 성능: 메서드 호출로 쿼리를 수행하는 것이 SQL 직접 작성보다 성능이 떨어질 수 있으나 현재는 많이 발전하여 격차가 적은 상태
2. 세밀함: 복잡한 분석 쿼리를 메서드만으로 해결하는 것이 힘들어 보완을 위해 SQL과 유사한 JPQL 지원 + SQL 자체 쿼리 작성 지원

## MyBatis

자바 오브젝트와 SQL 사이의 자동매핑 기능을 지원하는 ORM 프레임워크로 SQL을 별도의 파일로 분리하여 관리하게 해준다.

Hibernate, JPA와 달리 새로운 DB 프로그래밍 패러다임을 익힐 필요 없으며 SQL을 그대로 이용하며 JDBC 코드 작성의 불편함을 제거할 수 있다.

<aside>
👉 **JDBC**(Java Database Connectivity)
DB에 접근할 수 있도록 Java에서 제공하는 API
데이터베이스에서 자료를 쿼리하거나 업데이트하는 방법을 제공

</aside>

### 특징

1. 쉬운 접근성과 코드의 간결함
JDBC의 모든 기능 제공 + 깔끔한 소스코드 유지 가능
수동적 파라미터 설정과 쿼리 결과에 대한 매핑 구문 제거 가능
2. SQL문과 프로그래밍 코드 분리
3. 다양한 프로그래밍 언어로 구현 가능

### 주요 컴포넌트

![m1](https://user-images.githubusercontent.com/54874529/190563767-1f9f8f11-ec16-4f9b-9767-e4eda7aefb1e.png)

- MyBatis 설정파일(SqlMapConfig.xml) : VO 객체의 정보를 설정한다.
- SqlSessionFactory : MyBatis 설정파일을 바탕으로 SqlSessionFactory를 생성한다. Spring Bean으로 등록해야 한다.
- SqlSessionTemplate : 핵심적인 역할을 하는 클래스로서 SQL 실행이나 트랜잭션 관리를 실행한다. SqlSession 인터페이스를 구현하며, Thread-safe 하다. Spring Bean으로 등록해야 한다.
- Mapping 파일 (user.xml) : SQL문과 OR Mapping을 설정한다.
- Spring Bean 설정 파일 (mybatisBeans.xml) : SqlSessionFactoryBean을 Bean 등록할 때 DataSource 정보와 MyBatis Config 파일정보, Mapping 파일의 정보를 함께 설정한다. SqlSessionTemplate을 Bean으로 등록한다.

**장점**

1. SQL에 대한 모든 컨트롤할 때 적합
2. SQL 쿼리가 최적화되어 있을 때 유용

**단점**

1. 애플리케이션과 데이터베이스 간에 서로 잘 구조화되도록 많은 설정이 바뀌어야 하기 때문에 애플리케이션과 DB 간의 설계에 대한 모든 조작을 하고자 할 때 적합하지 않다.

---

⇒ 복잡한 쿼리(JOIN, GROUP BY) 사용으로 인한 복잡한 테이블 구조에서는 MyBatis를, 빠른 속도가 중요할 경우 JPA를 사용하자