# JPA

## JPA 등장 배경

SQL을 직접 다루면 발생하는 문제가 무엇이 있을까?

개발 중에 엔티티가 변경되면 그에 따라 수정해야 할 것들이 많다. 그렇기에 엔티티를 신뢰할 수 없다.
또한 SQL에 의존적인 개발을 피할 수가 없다.

## 패러다임 불일치

객체지향 프로그래밍에서는 추상화, 캡슐화, 정보은닉, 상속, 다형성 등 다양한 특징을 보유하고 있다. 

관계형 데이터베이스는 데이터 중심으로 구조화를 하기에 추상화와 상속, 다형성과 같은 개념이 존재하지 않는다. 

따라서 객체와 관계형 데이터베이스의 패러다임이 일치하지 않는다. 이러한 불일치를 해결하는 데 드는 시간과 코드 작성의 노력은 적지 않다.

위의 2가지 문제를 해결해주는 것이 JPA이다.

## JPA란

자바 진영의 ORM 기술 표준이다. 어플리케이션과 JDBC 사이에서 동작한다.

둘 사이에서 매핑을 해줌으로써 패러다임의 불일치를 해결해준다.

https://imsfromseoul.tistory.com/m/401