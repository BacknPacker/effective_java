# 🔥 [ Chapter4 ] 클래스와 인터페이스
**_추상화의 기본 단위인 클래스와 인터페이스는 자바 언어의 심장과도 같다._**

## 🎯  아이템 15. 클래스와 맴버의 접근 권한을 최소화하라.
> 오직 API를 통해서만 다른 컴포넌트와 소통하며 서로의 내부 동작 방식에는 전혀 개의치 않는다.<br>
> 정보 은닉, 혹은 <span style='color:red;'>캡슐화</span>라고 하는 이 개념은 소프트웨어 설계의 근간이 되는 원리이다.

<br>

⭐ **정보 은닉의 핵심 : 모든 클래스와 맴버의 접근성을 가능한 한 좁혀야 한다.**
<img width="60%" src="https://user-images.githubusercontent.com/55771326/164727088-978eab11-1382-4955-95f8-782185c93f5f.png">

<br>


⭐ **`public` 클래스의 인스턴스 필드는 되도록 `public`이 아니어야 한다.**
```java
public class User {
    private long userSn;
    private String userId;
    private String userPw;
}    
```

<br>


⭐ **`public` 가변 필드를 갖는 클래스는 일반적으로 스레드(멀티 스레드에) 안전하지 않다.**

**<span style='color:red;'>스레드 불안정</span> : 상태 제어가 잘못되면 프로그램은 불안정해져서 먹통이 되거나 다운되는것을 말한다.**


<br>


⭐ **클래스에서 `public static final` 배열 필드를 두거나 이 필드를 반환하는 접근자 메서드를 제공해서는 안되다.**
* **이전 코드**
```java
private static final Thing[] PRIVATE_VALUES = { ... };
public static final List<Thing> VALUES = Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUES));
```
* **변경 후 코드**
```java
private static final Thing[] PRIVATE_VALUES = { ... };
public static final Thing[] values(){
    return PRIVATE_VALUES.clone();    
}
```

> `public` 클래스는 상수용 `public static final` 필드 외에는 어떠한 `public` 필드도 가져서는 안된다.<br>
> `public static final` 필드가 참조하는 객체가 <span style='color:red;'>불변</span>인지 확인하라.
