# 2장. 객체 생성과 파괴

## 아이템1. 생성자 대신 정적 팩터리 메서드를 고려하라.
+ 정적 팩터리 메서드와 public 생성자는 각각의 장단점이 있기 때문에 잘 활용하라!
```java
public static Boolean valueOf(boolean b){
  return b ? Boolean.TRUE : Bloolean.FALSE;
}
```
### 장점
+ 이름을 가질 수 있다.
+ 호출될 때마다 인스턴스를 새로 생성하지는 않아도 된다.
+ 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.
+ 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.
+ 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체으 ㅣ클래스가 존재하지 않아도 된다.
### 단점    
+ 상속을 하려면 public이나 protected 생성자가 필요하니 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없다.
+ 정적 팩터리 메서드는 프로그래머가 찾기 어렵다.

## 아이템2. 생성자에 매개변수가 많다면 빌더를 고려하라.
+ 생성자나 메서드가 처리해야 할 파라미터가 많다면 빌더를 고려하자!
```java
// 점층적 생성자 패턴 ( 끔찍... )
Member m = new Member(0L, "quedevel", "1234", "김동호", "qudevel@innotree.com", "M", 90, 010, 0101, 0101);
// 자바빈즈 패턴
Member m2 = new Member();
m2.setMbrSn(0L);
m2.setMbrId("quedevel");
m2.setMbrPw("1234");
m2.setMbrNm("김동호");
m2.setEmail("qudevel@innotree.com");
m2.setGender("M");
m2.setAge(90);
m2.setHp1(010);
m2.setHp2(0101);
m2.setHp3(0101);

// 빌더 패턴
Member m3 = Member.builder()
        .mbrSn(0L)
        .mbrId("quedevel")
        .mbrPw("1234")
        .mbrNm("김동호")
        .email("qudevel@innotree.com")
        .gender("M")
        .age(90)
        .hp1(010).hp2(0101).hp3(0101)
        .build();
```
+ 빌더는 점층적 생성자보다 클라이언트 코드를 읽고 쓰기가 훨씬 간결하고, 자바빈즈보다 훨씬 안전하다.

## 아이템3. private 생성자나 열거 타입으로 싱글턴임을 보증하라.
+ 객체를 오직 하나만 생성하여 활용하는것을 싱글턴이라고 한다.
+ 싱글턴을 만들때 반드시 고려해야하는 사항은 무상태 객체이다.
## 아이템4. 인스턴스화를 막으려거든 private 생성자를 사용하라.
+ 객체 지향적으로 사고하지 않는 이들이 종종 남용하는 방식이지만 나름에 쓰임새가 있으니 고려해서 잘 활용하자

## 아이템5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라.
클래스가 내부적으로 하나 이상의 자원에 의존하고, 그 자원이 클래스 동작에 영향을 준다면
싱글턴과 정적 유틸리티 클래스는 사용하지 않는 것이 좋다.<br>
필요한 자원을 생성자에 (혹은 정적 팩터리나 빌더에) 넘겨주자.<br>
의존 객체 주입이라 하는 이 기법은 클래스의 유연성, 재사용성, 테스트 용이성을 개선해준다.
## 아이템6. 불필요한 객체 생성을 피하라.
+ 박싱된 기본 타입보다는 기본 타입을 사용하고, 의도치 않은 오토박싱이 숨어들지 않도록 주의하자.
## 아이템7. 다 쓴 객체 참조를 해제하라.
+ 메모리 누수는 작업 당시에도 놓치기 마련이니 예방법을 익혀두자
## 아이템8. finalizer와 cleaner 사용을 피하라.

Object.java 에 구현되어 있는 finalizer의 주석 참고
```java
/**
 * @deprecated The finalization mechanism is inherently problematic.
 * Finalization can lead to performance issues, deadlocks, and hangs.
 * Errors in finalizers can lead to resource leaks
 */
```

@deprecated finalizer 메커니즘은 본질적으로 문제가 있다.<br>
finalizer은 성능 문제, 교착 상태 및 중단으로 이어질 수 있습니다.<br>
finalizer의 오류는 리소스 누수로 이어질 수 있습니다.

cleaner 또한 성능에 문제를 줄 수 있으니 사용을 피하자!

## 아이템9. try-finally보다는 try-with-resources를 사용하라.
반드시 자원을 회수해야 하는 자원을 다룰때는<br>
try-finally를 이용해 close 함수를 사용하지 말고<br>
try-with-resources문을 활용해 정확성과 가독성을 높여서 자원을 회수하자

