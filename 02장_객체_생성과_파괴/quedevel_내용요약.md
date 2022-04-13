# 2장. 객체 생성과 파괴

## 아이템1. 생성자 대신 정적 팩터리 메서드를 고려하라.
+ 클래스는 생성자와 별도로 정적 팩터리 메서드를 제공할 수 있다.
+ 디자인 패턴의 팩터리 메서드와 무관하다.
```java
public static Boolean valueOf(boolean b){
  return b ? Boolean.TRUE : Bloolean.FALSE;
}
```
### 정적 팩터리 메서드의 장점
1. 반환 객체 특성을 제대로 묘사하는 이름을 지을 수 있다.
    + BigInteger(int,int,Random) vs BigInteger.probablePrime
    + 동일한 시그니처를 가지는 메서드를 만들고자 할 때

## 아이템2. 생성자에 매개변수가 많다면 빌더를 고려하라.
## 아이템3. private 생성자나 열거 타입으로 싱글턴임을 보증하라.
## 아이템4. 인스턴스화를 막으려거든 private 생성자를 사용하라.
## 아이템5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라.
## 아이템6. 불필요한 객체 생성을 피하라.
## 아이템7. 다 쓴 객체 참조를 해제하라.
## 아이템8. finalizer와 cleaner 사용을 피하라.
## 아이템9. try-finally보다는 try-with-resources를 사용하라.


