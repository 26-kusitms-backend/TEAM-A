# Spring Security

### Spring Security 정의
* Spring Security는 엔터프라이즈 애플리케이션에 대한 인증, 권한 부여 및 기타 보안 기능을 제공하는 Java / Java EE 프레임 워크입니다.

<br/>

### Spring Security 구조
![img1 daumcdn](https://user-images.githubusercontent.com/92728780/192766205-03702022-301e-4bb5-9944-2890f84f08cc.png)

<br/>

### Spring Security 동작 원리
* Spring Security는 기본적으로 세션 - 쿠키 방식 사용


![008i3skNgy1gq0pikfvdbj30ge0bxq37](https://user-images.githubusercontent.com/92728780/192767744-2bb4753b-36b4-4f30-b7ca-f4f9fa282fcc.jpg)

```
1. 클라이언트가 서버로 로그인 요청을 보낸다.

2. 서버는 클라이언트가 보낸 (id, pw)를 확인한다.

3. (4 포함) 요청 정보가 유효하면 세션 ID를 생성한다.

4. 클라이언트는 응답으로 받은 세션을 쿠키에 저장한다.

5. 클라이언트가 인증이 필요한 요청을 할 때마다 헤더에 쿠키 실어서 보낸다.

6. 서버는 쿠키를 확인하여 사용자를 식별합니다.

7. 사용자에게 맞는 데이터를 넘겨줍니다.
```
[Spring security 동작 원리 (인증,인가) 에 대한 자세한 설명](https://k3068.tistory.com/88?category=948029)
