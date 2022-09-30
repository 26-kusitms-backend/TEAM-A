# 📍 키 (Key)

데이터베이스에서 조건에 만족하는 튜플을 찾거나 순서대로 정렬할 때, 다른 튜플들과 구별할 수 있는 `유일한 기준`이 되는 속성

<br>

## Key Constraints

하나 또는 그 이상의 fields가 모여 key가 될 수 있다.

<br>

### 최소성
        
- `하나의 키에 대응하는 튜플은 오직 하나`여야 한다.
- `최소한` ← 의 조건을 만족해야한다. 다른 하나를 뺐을 때 여전히 튜플을 유일하게 식별할 수 있다면, 최소성을 만족하지 않는 것 !

```sql
'(주민등록번호, 나이, 사는 곳, 혈액형)' → '키(주민등록번호, 나이)'

=> 최소성을 만족하지 않는 키, 나이가 굳이 없더라도 튜플을 유일하게 식별할 수 있기 때문 !
```

<br>

### 유일성

- `하나의 키 값`으로 튜플을 `유일하게 식별`할 수 있어야 한다.

```sql
'(주민등록번호, 나이, 사는 곳, 혈액형)' → '키(주민등록번호)'

=> 유일성을 만족하는 키, 그 외의 속성들은 중복될 수 있기 때문에 유일성을 만족하지 X
```

<br>

---

<br>


## 슈퍼키 (Super Key)

- `유일성 O`, `최소성 X`
- ex. (학번 + 이름), (주민등록번호 + 나이)

<br>

## 후보키 (Candidate Key)

- `유일성 O`, `최소성 O`
- 기본키(Primary Key)가 될 수 있는 후보키
- 후보키 중 기본키로 선택받지 못한 키를 `대체키 (Alternate Key)`라고 한다.

```sql
CREATE TABLE Enrolled (
  sid CHAR(20)
  cid CHAR(20),
  grade CHAR(2),
  PRIMARY KEY (sid),
  UNIQUE (cid, grade) -- 보조키 선언 키워드 : UNIQUE
); 
```

<br>

## 기본키 (Primary Key)

- `유일성 O`, `최소성 O`
- 후보키 중 선택받은 키

```sql
CREATE TABLE Enrolled (
  sid CHAR(20)
  cid CHAR(20),
  grade CHAR(2),
  PRIMARY KEY (sid, cid) -- 기본키 선언 키워드 : PRIMARY KEY
);
```

<br>

### 기본키의 특징

여러 후보키 중 하나의 기본키를 선택함에 있어 다음의 기준을 통과해야한다.

1. NULL 값을 가질 수 있는 속성이 포함되어 있으면 안된다.
2. 값이 변경될 가능성이 높은 속성이 포함되어 있으면 안된다.
3. 단순한 후보키를 기본키로 선택한다.

<br>

## 외래키 (Foreign Key)

- 관계를 맺고 있는 다른 테이블의 키를 내 테이블에 `참조`해서 사용하는 키


```sql
CREATE TABLE Enrolled (
  sid CHAR(20)
  cid CHAR(20)
  grade CHAR(2),
  PRIMARY KEY (sid, cid),
  FOREIGN KEY (sid) REFERENCES Students (sid) -- 외래키 선언 키워드 : FOREIGN KEY ~ REFERENCES ~
);
```

<br><br>

# 📍 스프링에서의 기본키 매핑
## @Id
```java
@Entity
public class User {
    @Id
    private String id;
}
```
- 데이터베이스 테이블의 기본 키(PK)와 객체의 필드를 매핑시켜주는 어노테이션
- @GeneratedValue 어노테이션을 같이 사용해주지 않을 경우 기본 키를 직접 할당해주어야 한다.

<br>

## @GeneratedValue
```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private String id;
}
```
- 기본키를 자동으로 생성해주는 어노테이션

<br>

### 전략
1. `IDENTITY`
    - 기본키 생성을 데이터베이스에 위임하는 전략
    - 주로 MySQL, PostgreSQL, SQL Server에서 사용한다.
2. `SEQUENCE`
    - 시퀀스를 사용해서 기본키를 생성하는 전략
    - 시퀀스를 지원하는 Oracle, PostgreSQL, H2에서 사용할 수 있다.
    - 👇 사용법 👇
      ```java
      @Entity
      @SequenceGenerator( // 1. 시퀀스 생성기 등록
              name = "BOARD_SEQ_GENERATOR",
              sequenceName = "BOARD_SEQ",
              initialValue = 1,
              allocationSize = 1
      )
      public class User {
          @Id
          @GeneratedValue(strategy = GenerationType.SEQUENCE,
                          generator = "BOARD_SEQ_GENERATOR") // 2. 시퀀스 생성기 선택
          private Long id;
      }
      ```
    - SequenceGenerator 속성
      1. name : 시퀀스 생성기 이름
      2. sequenceName : 데이터베이스에 등록되어 있는 시퀀스 생성기 이름
      3. initialValue : 시퀀스의 시작값 (기본값 = 1)
      4. allocationSize
          - 시퀀스를 한 번 호출할 때 증가하는 수 (기본값 = 50)
          - DB 에는 미리 50을 올려놓고, 메모리상에서 1씩 올려서 만약 50까지 다사용했다면 다시 호출하여 DB의 값은 100으로 올려 이전 과정을 반복 ! -> 성능 최적화 !
          - 데이터베이스 시퀀스값이 1 씩 증가한다면 반드시 1로 지정해야 한다.
3. `TABLE`
    - 키 생성 전용 테이블을 사용하여 기본키를 생성하는 전략 (-> 시퀀스를 흉내내는 전략 !)
    - 테이블을 사용하므로 모든 데이터베이스에 적용할 수 있다.
    - 👇 사용법 👇
      ```java
      @Entity
      @TableGenerator( // 1. 테이블 생성기 등록
          name = "BOARD_SEQ_GENERATOR",
          table = "MY_SEQUENCES",
          pkColumnValue = "BOARD_SEQ", allocationSize = 1)
      public class Board {
          @Id
          @GeneratedValue(strategy = GenerationType.TABLE,
                          generator = "BOARD_SEQ_GENERATOR") // 2. 테이블 생성기 선택
          private Long id;
      }
     ```
4. `AUTO`
    - 선택한 데이터베이스의 방언에 따라 INDENTITY, SEQUENCE, TABLE 전략 중 하나를 자동으로 선택하는 전략
    - 데이터베이스를 변경해도 코드를 수정할 필요가 없다는 장점이 있다.
