# 📍 AOP(Aspect Oriented Programing)

- `관점 지향형` 프로그래밍
- 기존 OOP에서 바라보던 관점을 부가적인 측면에서 보았을 때 공통적인 측면을 추출한다는 개념 => 가로 영역의 공통된 부분을 잘랐다고 하여 Cross-Cutting이라고도 한다 !
- 반복되는 로직들을 모듈화하여 필요할 때 호출하여 사용하는 방법
- `코드 밖에서 설정`되며, 애플리케이션 전체에 걸쳐 사용되는 기능을 `재사용` 할 수 있다.

<br>

## AOP가 사용되는 경우
1. 간단한 메서드 성능 검사 : DB에 다량의 데이터를 넣고 빼는 등 배치 작업에 대한 시간 측정
2. Transaction
3. 예외 반환
4. 기타 : 로깅, 보안 등

<br>

## 용어 정리
- Aspect : 반복되어 사용되는 로직으로, 핵심 기능에 부가되어 의미를 갖는 특별한 모듈
- Target : Aspect를 부여할 대상, 핵심 로직을 담당
- Advice : 실질적인 핵심 로직의 구현체
- JointPoint, PointCut : Advice의 적용 위치
- Proxy : Target을 감싸 Target의 요청을 대신 받아주는 Wrapping Object

<br>

## 특징
1. `Proxy 패턴 기반`의 AOP 구현체
2. Spring Bean에만 AOP 적용 가능
3. 모든 AOP 기능을 제공하는 것이 아닌 스프링 컨테이너와 연동하여 가장 흔한 문제 (중복 코드, Proxy 클래스 작성의 번거로움, 객체들 간 관계 복잡도 증가)에 대한 해결책을 지원하는 것이 목적

<br><br>

# 📍 AOP에서의 Proxy

## Proxy

![image](https://user-images.githubusercontent.com/78673570/198841328-1496d0ec-a71f-42d8-92fd-67f73ddab500.png)

- `대리자` 역할로, 클라이언트가 사용하려고 하는 실제 대상인 것처럼 위장하여 대리하여 업무를 처리하는 역할
- Proxy를 통해 최종적으로 요청을 위임받아 처리하는 실제 오브젝트를 타겟(target) or 실체(real subject)라고 한다.
- Proxy 호출 및 처리 순서
  1. Proxy 호출
  2. 보조 업무 처리
  3. Proxy 처리 함수가 실제 구현 함수 호출 및 주 업무 처리
  4. 제어권이 다시 Proxy 함수로 넘어오고, 나머지 보조 업무 처리
  5. 처리 작업 완료 후 호출 함수로 반환

<br>

## AOP에서의 Proxy
스프링은 Proxy를 이용하여 AOP를 구현하는데, Aspect의 적용 대상이 되는 객체에 대한 Proxy를 만들어 제공하고 비즈니스 로직에 접근할 때 Target 객체에 바로 접근하는 것이 아니라 Proxy 객체를 통해서 간접적으로 접근한다.

=> Target 객체는 Bean 객체가 생성되는데, 런타임 시에 오브젝트 생성 설정에 따라 스프링 컨테이너가 지정한 Bean 객체에 대한 Proxy 객체를 생성하고, 원본 객체 대신 Proxy 객체를 사용하도록 한다.

<br>

## Proxy 객체 생성 방식
Target 객체가 인터페이스를 구현하고 있느냐/없느냐에 따라서 달라진다.

1. JDK Dynamic Proxy
2. CGLIB

<br>

### JDK Dynamic Proxy
- Dynamic Proxy = 런타임 시에 동적으로 만들어지는 오브젝트
- 인터페이스에 대한 명세를 기준으로 Proxy를 생성하는 방식
- java의 reflection을 이용해서 proxy 객체 생성(java.lang.reflect)
- Taget 인터페이스와 동일한 형태로 생성 (cf. 인터페이스가 없을 경우에는 CGLib 라이브러리를 사용해 Proxy 생성 가능)
