# 📍 리플렉션(Reflection)

- `구체적인 클래스 타입을 알지 못해도` 그 클래스의 메소드, 타입, 변수들에 접근할 수 있도록 해주는 자바 API
- 런타임에 지금 실행되고 있는 클래스를 가져와서 실행해야 하는 경우, 동적으로 객체를 생성하고 메서드를 호출하는 방법
- 코드를 작성할 시점에는 어떤 타입의 클래스를 사용할지 모르지만, `런타임 시점`에 지금 실행되고 있는 클래스를 가져와서 `동적으로` 실행해야 하는 경우에 사용한다.

<br>

## 관련 메소드

### 생성자 찾기

- getDeclaredConstructor() : 인자가 없는 생성자를 가져온다.
- getDeclaredConstructor(Param) : Param에 일치하는 타입의 생성자를 가져온다.
-  getConstructors() : Public 생성자를 가져온다.

<br>

### 메서드 찾기
- getDeclaredMethod() : 인자로 메소드와 파라미터 정보를 넘겨주면 일치하는 메소드를 가져온다.
- getDeclaredMethods() : 클래스의 모든 메소드들을 가져온다.
- getMethods() : public 메소드들을 가져온다.

<br><br>

# 📍 리플렉션으로 만드는 스프링의 Dynamic proxy

다음과 같은 인터페이스와 구현체가 있을 때,
```java
//Sample 인터페이스
interface Sample {
    void doSomething();
    String saySomething();
    String saySomething2();
}

//Sample의 구현체
public class SampleImpl implements Sample {
    public void doSomething(){
        System.out.println("do-something");
    }

    public String saySomething(){
        return "something";
    }

    public String saySomething2(){
        return "something2";
    }
}
```

<br>

Sample의 Proxy 객체를 생성하려면 👇

```java
//프록시 생성
public class SampleProxy implements Sample {
    Sample sample;

    public SampleProxy(Sample sample){
        this.sample = sample;
    }

    public void doSomething(){
        System.out.println("do-something in proxy");
        sample.doSomething();
    }

    public void saySomething(){
        sample.doSomething().toUpperCase();
    }

    public void saySomething2(){
        sample.doSomething2().toUpperCase();
    }
}
```
인터페이스에 대해 모든 메서드를 직접 구현해 주어야 하고, 동일한 액션이 있을 경우 중복해서 만들어주어야 한다.

<br>

리플렉션을 이용한다면 👇

```java
//리플렉션을 이용한 프록시 생성
public class SampleProxy implements InvocationHandler {
    Sample sample;

    public SampleProxy(Sample sample) {
        this.sample = sample;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        switch (method.getName()) {
            case "doSomething":
                System.out.println("do-something in proxy");
                return method.invoke(sample, args);
            case "saySomething":
            case "saySomething2":
                return ((String) method.invoke(sample, args)).toUpperCase();
            default:
                return method.invoke(sample, args);
        }
    }
}
```
리플렉션을 통해 인터페이스를 구현 함으로서 Proxy 객체를 동적으로 생성한다. (따라서 Dynamic Proxy를 사용할 때에는 반드시 인터페이스가 필요하다 !)
