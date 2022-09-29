# Spring Security

### Spring Security 정의
* Spring Security는 엔터프라이즈 애플리케이션에 대한 인증, 권한 부여 및 기타 보안 기능을 제공하는 Java / Java EE 프레임 워크입니다.

<br/>

### Spring Security 구조
![img1 daumcdn](https://user-images.githubusercontent.com/92728780/192766205-03702022-301e-4bb5-9944-2890f84f08cc.png)

```
동작 플로우

1. Http Request : 사용자가 로그인 정보와 함께 인증을 요청한다.

2. AuthenticationFilter가 요청을 가로 챈다. 가로친 정보를 바탕으로 UsernamePasswordAuthenticationToken 객체를 생성한다.

3. AuthenticationFilter가 AuthenticationManger의 구현체인 ProviderManager에게 토큰 (UsernamePasswordAuthenticationToken)을 전달한다.

4. 다시 AuthenticationProvider에 UsernamePasswordAuthenticationToken 객체를 전달합니다.

5. AuthenticationProvider가 실제 데이터베이스에서 사용자 인증정보를 가져오는 UserDetailsService에 사용자 정보(아이디)를 넘겨줍니다.

6. 넘겨받은 사용자 정보를 통해 DB에서 찾은 사용자 정보인 UserDetails 객체를 만듭니다.

7. AuthenticationProvider(인증 제공자) 는 UserDetails를 넘겨받고 사용자 정보를 비교합니다.

8. 인증이 완료되면 권한 등의 사용자 정보를 담은 Authentication 객체를 반환합니다. (인증 제공자 -> 인증 관리자)

9. AuthenticationFilter에 Authentication 객체가 반환됩니다. 

10. Authentication 객체를 SecurityContext에 저장합니다
```
[Spring Security 흐름](https://seongbindb.tistory.com/169)

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
