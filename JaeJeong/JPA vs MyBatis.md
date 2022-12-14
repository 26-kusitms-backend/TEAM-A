# ๐ JPA

- Java `ORM ๊ธฐ์ ` ๋ํ API ํ์ค ๋ช์ธ
- ๋ํ์ ์ธ ๊ตฌํ์ฒด๋ก๋ Hibernate
- ๊ธฐ๋ณธ์ ์ผ๋กCRUD ๋ฉ์๋๋ฅผ ์ ๊ณตํ๋ค.
- 1์ฐจ ์บ์ฑ, ์ฐ๊ธฐ์ง์ฐ, ๋ณ๊ฒฝ๊ฐ์ง, ์ง์ฐ๋ก๋ฉ์ ์ ๊ณตํ๋ค.
- MyBatis๋ ์ฟผ๋ฆฌ๊ฐ ์์ ๋์ด ๋ฐ์ดํฐ ์ ๋ณด๊ฐ ๋ฐ๋๋ฉด ๊ทธ์ ์ฌ์ฉ ๋๊ณ  ์๋ DTO์ ํจ๊ป ์์ ํด์ฃผ์ด์ผ ํ๋ ๋ฐ๋ฉด์, JPA ๋ ๊ฐ์ฒด๋ง ๋ฐ๊พธ๋ฉด ๋๋ค.
- ๋ณต์กํ ์ฟผ๋ฆฌ๋ ํด๊ฒฐ์ด ์ด๋ ต๋ค.

<br><br>

# ๐ MyBatis

- ์๋ฐ์์ `SQL Mapper`๋ฅผ ์ง์ํด์ฃผ๋ ํ๋ ์์ํฌ๋ก `Object Mapping` ๊ธฐ์ 
- SQL๋ฌธ์ ์ด์ฉํด์ RDBMS์ ์ ๊ทผํ๊ณ  ๋ฐ์ดํฐ๋ฅผ ๊ฐ์ฒดํํ๋ค.
- SQL์ ์ง์  ์์ฑํ์ฌ ์ฟผ๋ฆฌ ์ํ ๊ฒฐ๊ณผ๋ฅผ ๊ฐ์ฒด์ ๋งคํํ๋ค.
- ๊ฐ์ฒด์ ์ฟผ๋ฆฌ๋ฌธ ๋ชจ๋ ๊ด๋ฆฌํด์ผํ๊ณ , CRUD ๋ฉ์๋๋ฅผ ์ง์  ๊ตฌํํด์ผํ๋ค.
- ์ฟผ๋ฆฌ๋ฌธ์ xml๋ก ๋ถ๋ฆฌํ  ์ ์๋ค.
- ๋ณต์กํ ์ฟผ๋ฆฌ๋ฌธ์ ์์ฑํ  ์ ์๋ค.

<br><br>

# ๐ JDBS vs JPA vs MyBatis

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
- ์ฟผ๋ฆฌ๋ฌธ์ ์ง์  ์์ฑํ๊ณ  ๊ฐ๊ฐ์ ์ฟผ๋ฆฌ ํ๋ผ๋ฏธํฐ์ ์ฌ์ฉ์ ๋ฐ์ดํฐ๋ฅผ ์ง์  Setํด์ ๋ฐ์ดํฐ๋ฒ ์ด์ค์ ์ ์ฅํ๋ค.
- ๊ฐ๋์ฑ์ด ๋จ์ด์ง๊ณ , ์์์ด ๋ถํธํ๋ค.

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
- ๋ฐ์ดํฐ๋ฒ ์ด์ค ์ฒ๋ฆฌ๋ฅผ ์ํ Mapper ์ธํฐํ์ด์ค์ Mapper.xml ํ์ผ์ ์ฌ์ฉํ๋ค.
- Repository์์ ๋ฉ์๋์ ์ด๋ฆ์ ์ฟผ๋ฆฌ id์ ๋งคํ๋๋ฉฐ, xml ํ์ผ์์ ํด๋น ์ฟผ๋ฆฌ์ id๋ฅผ ๊ธฐ์ค์ผ๋ก ์ฟผ๋ฆฌ๋ฅผ ์คํํ๋ค.
- PreparedStatement๋ฅผ ์ง์  ์ฒ๋ฆฌํด์ผํ๋ ์ ํต์ ์ธ JDBC ๋ฐฉ๋ฒ๊ณผ ๋ฌ๋ฆฌ, MyBatis ๋ด๋ถ์ ์ผ๋ก PreparedStatement๋ฅผ ์ฒ๋ฆฌํ๋ค.

<br>

## JPA

```java
//์ํฐํฐ
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
//๋ ํฌ์งํ ๋ฆฌ
public interface UserRepository extends JpaRepository <User, Long> {
}
```

```java
//์๋น์ค
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
        user.setName("๋ณ๊ฒฝ๋ ์ด๋ฆ");
    }

}
```
- JPA๋ Repository ๋จ๊ณ์์ ๊ตฌํ๋์ด ์์ผ๋ฉฐ, MyBatis์ ๋ฌ๋ฆฌ ์ฟผ๋ฆฌ๋ฅผ ์ง์  ์์ฑํ์ง ์๋๋ค.
- findUserAndUpdateName ๋ฉ์๋ <br> 
: ๋ณ๋์ update ์ฟผ๋ฆฌ๋ฅผ ์์ฑํ์ง ์์๋ JPA์ ์ํด DB์์ ์กฐํํ ๋ฐ์ดํฐ๋ค์ด ๊ฐ์ฒด๋ก ์ฐ๊ฒฐ๋์ด ์๊ณ  ๊ฐ์ฒด์ ๊ฐ์ ์์ ํ๋ ๊ฒ์ ๋ฐ์ดํฐ๋ฒ ์ด์ค์ ๊ฐ์ ์์ ํ๋ ๊ฒ์ด๋ฏ๋ก ๋ฉ์๋๊ฐ ์ข๋ฃ๋  ๋ update ์ฟผ๋ฆฌ๊ฐ ์คํ๋๋ค.
