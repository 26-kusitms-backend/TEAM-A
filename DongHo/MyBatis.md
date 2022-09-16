# MyBatis란?


## MyBatis의 정의
- 자바 퍼시스턴스 프레임워크의 하나로 XML 서술자나 애너테이션(annotation)을 사용하여 저장 프로시저나 SQL 문으로 객체들을 연결시킨다. (SQL Mapper)

 

## MyBatis를 사용하는 이유 (특징)
### 1. 쉬운 접근성과 코드의 간결함
- JDBC의 모든 기능을 Mybatis가 대부분 제공한다.
- 복잡한 JDBC코드를 걷어내며 깔끔한 소스코드를 유지할 수 있다.
- 수동적인 파라미터 설정과 쿼리 결과에 대한 맵핑 구문을 제거할 수 있다.

### 2. SQL문과 프로그래밍 코드의 분리
- SQL에 변경이 있을 때마다 자바 코드를 수정하거나 컴파일하지 않아도 된다. (XML)

## MyBatis 아키텍쳐
![다운로드](https://user-images.githubusercontent.com/92728780/190394162-4ffbe0ca-320c-4037-a847-5c33311326cf.jpg)


## MyBatis Annotation 방식

### Service
```java
@Autowired
IndexMapper indexMapper;

public void myAnnotation() {
    String testTxt = indexMapper.myAnnotation();
    System.out.println("annotation으로 가져온 값 : " + testTxt);
}
```
### Mapper
```java
@Mapper
public interface IndexMapper {

    @Select("select TIMESTAMP from APPQOSSYS.WLM_CLASSIFIER_PLAN")
    public String myAnnotation();

}
```



## MyBatis XML 방식

### Service
```java
@Autowired
IndexMapper indexMapper;

public void myXML() {
    System.out.print("--------------server started--------------");
    String testTXT = indexMapper.myXML();
    System.out.println("myXML 결과 : " + testTXT);
}
```

### Mapper
```java
public String myXML();
}
```

### XML
```java
<mapper namespace="com.example.demo.mapper.IndexMapper">
    <select id="myXML" resultType="string">
        select "TIMESTAMP" from APPQOSSYS.WLM_CLASSIFIER_PLAN
    </select>
</mapper>
```





출처 : https://velog.io/@changyeonyoo/Mybatis%EB%9E%80-%EC%9E%A5%EC%A0%90-%ED%8A%B9%EC%A7%95-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8
