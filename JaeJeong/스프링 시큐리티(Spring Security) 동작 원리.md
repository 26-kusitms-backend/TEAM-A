# 📍 Spring Security 란?

Spring 기반의 애플리케이션의 보안 (인증, 인가 등)을 담당하는 스프링 하위 프레임워크

-   인증 (Authentication) : 현재 유저가 누구인지 확인
-   인가 (Authorization) : 현재 유저가 요청한 자원에 접근 가능한지(= 권한을 가지고 있는지)를 확인
-   접근 주체 (Principal) : 자원에 접근하는 유저
-   비밀번호 (Credential) : 자원에 접근하는 유저의 비밀번호

<br>

## 인증 방식

Spring Security는 기본적으로 `인증 (Authentication) → 인증 성공 후 → 인가 (Authorization)` 로 진행되며, `Credential 기반의 인증 방식` 을 사용한다.

<br>

💡 인증 방식
1. 사용자명(Principle), 비밀번호(Credential)로 인증하는 Credential 기반 인증  
2. OTP와 같은 추가적인 인증방식을 통해 2가지 방법으로 인증하는 이중 인증
3. 토큰 인증 (+ OAuth2)

<br>

## 특징

-   보안과 관련한 많은 옵션을 제공하여, 개발자가 보안 관련 로직을 일일이 작성하지 않아도 된다.
-   Annotation을 통해 간단하게 설정할 수 있다.
-   인증과 권한에 대한 부분을 Filter의 흐름에 따라 처리한다. 

<br><br>

# 📍 동작 원리

![image](https://user-images.githubusercontent.com/78673570/182030722-db9609ae-42f6-45b8-8876-4f6ed58b7dfd.png)

## 1. 인증 요청

-   사용자가 form을 통해 아이디/비밀번호를 입력하고 요청을 보낸다.
-   chain으로 구성된 Filter들의 doFilter() 메소드들이 순차적으로 호출된다.
-   아이디/비밀번호를 사용하는 form 기반 인증을 처리하는 필터인 UsernamePasswordAuthenticationFilter에 도착한다.

<br>

## 2. 토큰 생성

-   UsernamePasswordAuthenticationFilter의 attempAuthentication() 메소드가 동작하여, 요청으로부터 아이디/비밀번호를 가지고 와 `인증 객체(=UsernamePasswordAuthenticationToken)를 생성한다.
-   UsernamePasswordAuthenticationToken은 해당 요청을 처리할 수 있는 Provider를 찾는 데 사용된다.

<br>

## 3. Authentication Manager에게 처리 위임

-   AuthenticationFilter로부터 인증 객체(=UsernamePasswordAuthenticationToken)를 전달받아 Authentication Manager에게 처리를 위임한다.
-   Authentication Manager의 실질적인 구현체는 ProviderManager
-   Authentication Manager는 List 형태로 Provider들을 가지고 있다.

<br>

## 4. 적절한 Authentication Provider 선택

-   Authentication Manager는 가지고 있는 Authentication Provider들을 차례로 조회하여 인증을 요청한다.

<br>

## 5 ~ 9. 인증 절차

-   Authentication Provider가 실행되고, UserDetailsService를 통해 사용자 정보를 조회하여 로그인 유저 정보와 DB의 유저 정보가 일치하는지 확인한다.
-   UserDetailsService에서는 loadUserByUsername() 메소드를 호출하여 DB의 사용자 정보를 UserDetails 타입으로 가져온다. 만약 사용자 정보가 존재하지 않으면 예외가 발생한다. 
-   인증이 완료되면 Authentication 객체(=인증 객체=UsernamePasswordAuthenticationToken)를 전달하고, 그렇지 않으면 예외가 발생한다.

<br>

## 10. 인증 완료

-   회원 정보를 담아 리턴된 Authentication 객체를 SecurityContextHolder 객체 안의 SecurityContext에 저장한다.
-   인증 객체를 전역적으로 불러와서 사용할 수 있다.  
    (SecurityContextHolder를 통해서 Authentication 정보를 조회할 수 있다.)
