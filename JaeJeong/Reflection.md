# π λ¦¬νλ μ(Reflection)

- `κ΅¬μ²΄μ μΈ ν΄λμ€ νμμ μμ§ λͺ»ν΄λ` κ·Έ ν΄λμ€μ λ©μλ, νμ, λ³μλ€μ μ κ·Όν  μ μλλ‘ ν΄μ£Όλ μλ° API
- λ°νμμ μ§κΈ μ€νλκ³  μλ ν΄λμ€λ₯Ό κ°μ Έμμ μ€νν΄μΌ νλ κ²½μ°, λμ μΌλ‘ κ°μ²΄λ₯Ό μμ±νκ³  λ©μλλ₯Ό νΈμΆνλ λ°©λ²
- μ½λλ₯Ό μμ±ν  μμ μλ μ΄λ€ νμμ ν΄λμ€λ₯Ό μ¬μ©ν μ§ λͺ¨λ₯΄μ§λ§, `λ°νμ μμ `μ μ§κΈ μ€νλκ³  μλ ν΄λμ€λ₯Ό κ°μ Έμμ `λμ μΌλ‘` μ€νν΄μΌ νλ κ²½μ°μ μ¬μ©νλ€.

<br>

## κ΄λ ¨ λ©μλ

### μμ±μ μ°ΎκΈ°

- getDeclaredConstructor() : μΈμκ° μλ μμ±μλ₯Ό κ°μ Έμ¨λ€.
- getDeclaredConstructor(Param) : Paramμ μΌμΉνλ νμμ μμ±μλ₯Ό κ°μ Έμ¨λ€.
-  getConstructors() : Public μμ±μλ₯Ό κ°μ Έμ¨λ€.

<br>

### λ©μλ μ°ΎκΈ°
- getDeclaredMethod() : μΈμλ‘ λ©μλμ νλΌλ―Έν° μ λ³΄λ₯Ό λκ²¨μ£Όλ©΄ μΌμΉνλ λ©μλλ₯Ό κ°μ Έμ¨λ€.
- getDeclaredMethods() : ν΄λμ€μ λͺ¨λ  λ©μλλ€μ κ°μ Έμ¨λ€.
- getMethods() : public λ©μλλ€μ κ°μ Έμ¨λ€.

<br><br>

# π λ¦¬νλ μμΌλ‘ λ§λλ μ€νλ§μ Dynamic proxy

λ€μκ³Ό κ°μ μΈν°νμ΄μ€μ κ΅¬νμ²΄κ° μμ λ,
```java
//Sample μΈν°νμ΄μ€
interface Sample {
    void doSomething();
    String saySomething();
    String saySomething2();
}

//Sampleμ κ΅¬νμ²΄
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

Sampleμ Proxy κ°μ²΄λ₯Ό μμ±νλ €λ©΄ π

```java
//νλ‘μ μμ±
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
μΈν°νμ΄μ€μ λν΄ λͺ¨λ  λ©μλλ₯Ό μ§μ  κ΅¬νν΄ μ£Όμ΄μΌ νκ³ , λμΌν μ‘μμ΄ μμ κ²½μ° μ€λ³΅ν΄μ λ§λ€μ΄μ£Όμ΄μΌ νλ€.

<br>

λ¦¬νλ μμ μ΄μ©νλ€λ©΄ π

```java
//λ¦¬νλ μμ μ΄μ©ν νλ‘μ μμ±
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
λ¦¬νλ μμ ν΅ν΄ μΈν°νμ΄μ€λ₯Ό κ΅¬ν ν¨μΌλ‘μ Proxy κ°μ²΄λ₯Ό λμ μΌλ‘ μμ±νλ€. (λ°λΌμ Dynamic Proxyλ₯Ό μ¬μ©ν  λμλ λ°λμ μΈν°νμ΄μ€κ° νμνλ€ !)
