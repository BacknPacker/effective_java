# 7장. 람다와 스트림

## 아이템 42. 익명 클래스보다는 람다를 사용하라.
### 함수 객체
+ 추상 메서드를 하나만 담은 인터페이스(@FunctionalInterface, java8)의 인스턴스
### 익명 클래스 Anonymous Class
+ 함수 객체를 표현하는 방식으로 사용되는 Inner Class
+ 주로 해당 클래스에서만 사용하고 다른 클래스에서 사용되지 않는 경우
+ 익명 클래스 방식은 코드가 너무 길기 때문에 자바는 함수형 프로그래밍에 적합하지 않았다.
```java
Collection.sort(words, new Comparator<String>() {
  public int compare(String s1, String s2){
    return Integer.compare(s1.length(), s2.length());
  }
});
```
### 람다
+ 함수형 인터페이스의 인스턴스를 람다식으로 더 간결하게 표현할 수 있다.
+ 타입 추론은 컴파일러가 제네릭으로 대신 파악
```java
Collection.sort(words, (s1,s2) -> Integer.compare(s1.length(), s2.length()));
```
##### enum 상수별 추상 메서드 구현
```java
public enum Operation {
    PLUS("+") {
        public double apply(double x, double y) {
            return x + y;
        }
    };

    private final String symbol;

    Operation(String symbol) {
        this.symbol = symbol;
    }

    public abstract double apply(double x, double y);
}
```
```java
public enum OperationLambda {
    PLUS("+", (x, y) -> x + y);

    private final String symbol;
    private final DoubleBinaryOperator op;

    OperationLambda(String symbol, DoubleBinaryOperator op) {
        this.symbol = symbol;
        this.op = op;
    }

    public double apply(double x, double y){
        return op.applyAsDouble(x,y);
    }
}
```
##### 람다 사용시 주의점
+ 람다는 이름이 없으므로 문서화가 어렵다.
+ 열거 타입 생성자에게 넘겨지는 인수들의 타입도 컴파일 타임에 추론된다. 따라서 열거 타입 생성자 안의 람다는 열거타입의 인스턴스 멤버에 접근할 수 없다.
```java
public enum OperationLambda {
    PLUS("+", (x, y) -> System.out.println(symbol);   // ❗️ 열거타입 인스턴스 멤버 접근 불가
                        return x + y;);

    private final String symbol;
    private final DoubleBinaryOperator op;

    OperationLambda(String symbol, DoubleBinaryOperator op) {
        this.symbol = symbol;
        this.op = op;
    }

    public double apply(double x, double y){
        return op.applyAsDouble(x,y);
    }
}
```
##### 람다가 대체 할 수 없는것
+ 추상클래스의 인스턴스를 만들때 람다사용은 불가능하다.
+ 추상메서드가 여러 개인 인터페이스의 인스턴스도 람다로 표현 불가능하다.
+ 람다의 this는 바깥 인스턴스를 가리킨다.
  + 함수 객체가 자기 자신을 참조하려는 경우는 익명 클래스를 써야 한다.
+ 직렬화해야만 하는 함수객체가 있다면 private 정적 중첩 클래스의 인스턴스를 사용하자.  

<br><hr><br>
