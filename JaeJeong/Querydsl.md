JPA로 다중 조건 검색을 하려고 한다면 ▼
```java
public interface BoardRepository extends JpaRepository<Board, Long> {
    List<Board> findByTitleContainingOrContentContainingOrBookTitleContainingOrBookAuthorContaining(String keyword1, String keyword2, String keyword3, String keyword4);
}
```

이런 경우처럼 JPA 메소드를 사용하면 편리하게 where 조건을 추가할 수 있지만, 검색의 조합이 다양해지면 이해하기 어려워지고 경우에 따라 같은 기능의 메서드임에도 불구하고 메서드를 여러 개 구현해야 하는 문제가 발생할 수 있다.

<br>

이를 해결하기 위한 방법으로 Specification과 QueryDSL이 있는데 실무에서는 다음과 같은 이유로 Specification보다는 QueryDSL 사용을 권장한다고 한다.

💡 Specification의 특징
1. 작성한 코드의 SQL을 유추하기가 어려워 코드의 가독성이 부족하다.
2. 명세 정의 코드가 복잡하기 때문에 유지 보수에 좋지 않다.

<br><br>

# 📍 QueryDsl
- 정적 타입을 이용해서 SQL과 같은 쿼리를 생성할 수 있도록 해주는 프레임워크
- 문자가 아닌 코드로 쿼리를 작성함으로써, 컴파일 시점에 문법 오류를 쉽게 확인할 수 있다.
- 동적인 쿼리 작성이 편리하다.
- 쿼리 작성 시 제약 조건 등을 메서드 추출을 통해 재사용할 수 있다.

<br>

## 사용법
```java
@Override
public List<Care> searchCareHistory(User user, CareSearchFilter careSearchFilter) {
    return jpaQueryFactory
            .selectFrom(care)
            .where(
                    eqUser(user),
                    eqCategory(careSearchFilter.getCareCategoryCodeList())
            )
            .orderBy(sorting())
            .fetch();
}
```

<br>

### fetch()
```java
@Override
@SuppressWarnings("unchecked")
public List<T> fetch() {
    try {
        Query query = createQuery();
        return (List<T>) getResultList(query);
    } finally {
        reset();
    }
}
```
- query를 생성하고, 결과를 리스트로 반환하는 역할

<br>

### where()
```java
public Q where(Predicate... o) {
    return queryMixin.where(o);
}
```
- Predicate 타입의 객체를 인자로 받고, query를 Predicate에 맞춰서 생성한다.
- 생성하려는 조건의 개수대로 Predicate를 생성해서 파라미터로 넘겨주면 그에 맞는 Query가 생성된다.
- null을 파라미터로 넘겨주면 해당하는 파라미터를 무시하고 where 조건을 생성하지 않는다.

<br>

💡 Predicate 객체를 넘기는 방법
1. BooleanExpression 
```java
public abstract class BooleanExpression extends LiteralExpression<Boolean> implements Predicate {
}
```
- `post.user.eq(user);`와 같이 표현식의 결과로 반환되는 값
- and, or 등의 메소드를 사용해 BooleanExpression을 조합해 새로운 BooleanExpression을 만들 수 있어 재사용성이 높다.
- null을 반환하게되면 조건이 무시되기 때문에 안전하다.

<br>

2. BooleanBuilder 
```java
public final class BooleanBuilder implements Predicate, Cloneable  {
}
```
- BooleanExpression의 표현식들을 모아서 사용할 수 있도록 해주는 것
- 어떤 쿼리가 나가는지 예측하기 힘들다는 단접이 있다.
- `booleanBuilder.or(care.careCategoryCode.eq(careCategory));`

<br><br>

# 📍 JPA vs QueryDSL
- JPA 자체로 인한 성능 저하 이슈는 거의 없다.
  => JPA의 성능 이슈 대부분은 JPA를 잘 이해하지 못해서 발생한다. (ex. N+1 문제 등)
- 단순한 기능은 Data JPA, 복잡한 쿼리/동적 쿼리가 필요할 경우 QueryDSL을 채택
