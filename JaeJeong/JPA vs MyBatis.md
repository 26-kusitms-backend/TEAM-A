# 📍 JPA

- Java `ORM 기술` 대한 API 표준 명세
- 대표적인 구현체로는 Hibernate
- 기본적으로CRUD 메소드를 제공한다.
- 1차 캐싱, 쓰기지연, 변경감지, 지연로딩을 제공한다.
- MyBatis는 쿼리가 수정되어 데이터 정보가 바뀌면 그에 사용 되고 있던 DTO와 함께 수정해주어야 하는 반면에, JPA 는 객체만 바꾸면 된다.
- 복잡한 쿼리는 해결이 어렵다.

<br><br>

# 📍 MyBatis

- 자바에서 `SQL Mapper`를 지원해주는 프레임워크로 `Object Mapping` 기술
- SQL문을 이용해서 RDBMS에 접근하고 데이터를 객체화한다.
- SQL을 직접 작성하여 쿼리 수행 결과를 객체와 매핑한다.
- 객체와 쿼리문 모두 관리해야하고, CRUD 메소드를 직접 구현해야한다.
- 쿼리문을 xml로 분리할 수 있다.
- 복잡한 쿼리문을 작성할 수 있다.

<br><br>

# 📍 JDBS vs JPA vs MyBatis

## JDBC

```java
public void insertUser(User user){
      String query = " INSERT INTO USER (EMAIL, NAME, PW) VALUES (?, ?, ?)";

      PreparedStatement preparedStmt = conn.prepareStatement(query);
      preparedStmt.setString(1, user.getEmail());
      preparedStmt.setString(2, user.getName());
      preparedStmt.setString(3, user.getPW());

      preparedStmt.execute();
 }
```
- 쿼리문을 직접 작성하고 각각의 쿼리 파라미터에 사용자 데이터를 직접 Set해서 데이터베이스에 저장한다.
- 가독성이 떨어지고, 작업이 불편하다.

<br>

## MyBatis

```java
@Mapper
@Repository
public interface UserMapper {
    insertUser(User user);
}
```
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="my.batis.user.userMapper">
    <insert id="insertUser" parameterType = "user">
        INSERT USER(
            email, name, pw, 
        )VALUES(
            #{email}, #{name}, #{pw}
        )
    </insert>
</mapper>
```
- 데이터베이스 처리를 위한 Mapper 인터페이스와 Mapper.xml 파일을 사용한다.
- Repository에서 메소드의 이름은 쿼리 id에 매핑되며, xml 파일에서 해당 쿼리의 id를 기준으로 쿼리를 실행한다.
- PreparedStatement를 직접 처리해야하는 전통적인 JDBC 방법과 달리, MyBatis 내부적으로 PreparedStatement를 처리한다.

<br>

## JPA

```java
//엔티티
@Entity
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String email;
    private String name;
    private String pw;
}
```

```java
//레포지토리
public interface UserRepository extends JpaRepository <User, Long> {
}
```

```java
//서비스
@Service
@RequiredArgsConstructor
public class UserService {

    private final UserRepository userRepository;

    @Transactional
    public User newUser(String email, String name, String pw) {
        User user = new User();
        user.setEmail(email);
        user.setName(name);
        user.setPw(pw);
        return userRepository.save(user);
    }
    
    @Transactional
    public void findUserAndUpdateName(Long id) {
        User user = userRepository.findById(id);
        user.setName("변경된 이름");
    }

}
```
- JPA는 Repository 단계에서 구현되어 있으며, MyBatis와 달리 쿼리를 직접 작성하지 않는다.
- findUserAndUpdateName 메소드 <br> 
: 별도의 update 쿼리를 작성하지 않아도 JPA에 의해 DB에서 조회한 데이터들이 객체로 연결되어 있고 객체의 값을 수정하는 것은 데이터베이스의 값을 수정하는 것이므로 메소드가 종료될 때 update 쿼리가 실행된다.
