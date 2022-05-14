# 6장. 열거 타입과 애너테이션 (enum, annotation)
<br>

## 아이템 34. int 상수 대신 열거 타입을 사용하라.

##### 정수/문자열 열거 패턴의 단점
+ 다시 컴파일하지 않으면 기존에 파일 값이 그대로 새겨진다.
> 정수 상수는 문자열로 출력하기가 다소 까다롭다. 
> 그 값을 출력하거나 디버거로 살펴보면 단지 숫자로만 보여서 썩 도움이 되지 않는다.
> 같은 정수 열거 그룹에 속한 모든 상수를 한 바퀴 순회하는 방법도 마땅치 않다.
### 왜 열거 타입을 사용해야 하는가?
```java
public enum Apple {FUJI, PIPPIN, GRANNY_SMITH}
public enum Orange {NAVEL, TEMPLE, BLOOD}
```
+ 자바의 열거타입은 완전한 형태의 클래스라서 다른 언어의 열거타입보다 훨씬 강력하다.
##### 1. 밖에서 접근할 수 있는 생성자를 제공하지 않아 사실상 final이다. 
+ 인스턴스 통제, 싱글턴을 일반화한 형태
##### 2. 열거타입은 컴파일타임 타입 안전성을 제공
+ 다른 타입의 값을 넘기려 하면 컴파일오류가 난다.
##### 3. 열거타입에는 각자의 이름공간이 있다.
+ 이름이 같은 상수도 공존
+ 새로운 상수 추가나 순서 변경도 다시 컴파일할 필요가 없다.
+ 공개되는 것이 오직 필드의 이름뿐이 아니라, 정수 열거 패턴과 달리 상수 값이 클라이언트로 컴파일되어 각인되지 않기 때문이다.
##### 4. 열거타입의 toString 메서드는 출력하기에 적합한 문자열을 내어준다.
##### 5. 임의의 메서드나 필드를 추가할 수 있고 임의의 인터페이스를 구현하게 할 수 있다.
+ Object 메서드, Comparable, Serializable 구현

### 열거타입의 메서드나 필드 추가
```java
public enum Planet {
    MERCURY(3.302e+23,2.439e6),
    VENUS(4.869e+24,6.052e6),
    EARTH(5.975e+24, 6.378e6),
    MARS(6.419e+23,3.393e6),
    JUPITER(1.899e+27,7.149e7),
    SATURN(5.685e+26,6.027e7),
    URAUS(8.683e+25,2.556e7),
    NEPTUNE(1.024e+26,2.477e7);

    private final double mass;
    private final double radius;
    private final double surfaceGravity;

    private static final double G = 6.67300E-11;

    Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
        this.surfaceGravity = G * mass / (radius * radius);
    }

    public double mass() {
        return mass;
    }

    public double radius() {
        return radius;
    }

    public double surfaceGravity() {
        return surfaceGravity;
    }
    
    public double surfaceWeight(double mass) {
        return mass * surfaceGravity;
    }
}
```
+ 필드를 public으로 선언해도 되지만, private로 두고 별도의 public 접근자 메서드를 두는 게 낫다. (아이템 16)
+ 열거타입을 선언한 클래스 혹은 그 패키지에서만 유용한 기능은 private이나 package-private 메서드로 구현한다.
##### 널리 쓰이는 열거타입은 톱레벨 클래스로
+ java.math.RoundingMode, BigDecimal
+ RoundingMode는 BigDeciaml 외에도 사용되므로 탑레벨 클래스로 구분
```java
Class BigDecimal
java.lang.Object
  java.lang.Number
    java.math.BigDecimal

Class RoundingMode
java.lang.Object
  java.lang.Enum<RoundingMode>
    java.math.RoundingMode
```
### 상수별 메서드 구현
+ 상수별 메서드 구현은 상수 추가시에도 apply 수정하지 않으면 추상화 메서드이므로 컴파일시 에러를 알려준다.
```java
public enum Operation {
  PLUS {public double apply(double x, double y){return x+y;}},
  MINUS {public double apply(double x, double y){return x-y;}},
  TIMES {public double apply(double x, double y){return x*y;}},
  DIVIDE {public double apply(double x, double y){return x/y;}}
  
  public abstract double apply(double x, double y);
}
```
+ 열거타입에는 상수 이름을 입력받아 그 이름에 해당하는 상수를 반환해주는 valueOf(String) 메서드가 자동 생성된다.
    + Operation.valueOf("Plus")
```java
public enum Operation {
    PLUS("+") {
        public double apply(double x, double y) {
            return x + y;
        }
    },
    MINUS("-") {
        public double apply(double x, double y) {
            return x - y;
        }
    },
    TIMES("*") {
        public double apply(double x, double y) {
            return x * y;
        }
    },
    DIVDE("/") {
        public double apply(double x, double y) {
            return x / y;
        }
    };

    private final String symbol;

    Operation(String symbol) {
      this.symbol = symbol;
    }

    @Override
    public String toString() {
        return symbol;
    }

    public abstract double apply(double x, double y);
}
```
```java
public static void main(String[] args){
  douvle x = Double.parseDouble(args[0]);
  douvle y = Double.parseDouble(args[1]);
  for(Operation op:Operation.values())
    System.out.printf("%f %s %f = %f%n", x, op, y, op.apply(x,y));
}
```

### fromString 메서드    
+ 열거 타입의 toString 메서드를 재정의하려거든, toString이 반환하는 문자열을 해당 열거타입 상수로 변환해주는 fromString 메서드도 함께 제공하는 걸 고려해보자.
```java
private static final Map<String, Operation> stringToEnum =
            Stream.of(values()).collect(toMap(Object::toString, e -> e));

//Optional로 반환하여 값이 존재하지않을 상황을 클라이언트에게 알린다.
public static Optional<Operation> fromString(String symbol) {
    return Optional.ofNullable(stringToEnum.get(symbol));
}
```

<br><hr><br>

### 전략 열거타입 패턴
+ 상수별 메서드 구현에서는 열거 타입 상수끼리 코드를 공유하기 어렵다는 단점이 있다.
+ 평일 잔업수단 '전략'을 선택하게 한다.
    + 잔업수당 계산을 private 중첩 열거 타입으로 옮기고 PayrollDay 열거 타입의 생성자에서 이 중 적당한 것을 택한다.
    + 그러면 PayrollDay 열거 타입은 잔업수당 계산을 그 전략 열거 타입에 위임하여, switch 문이나 상수별 메서드 구현이 필요없게 된다.
```java
public enum PayrollDay {
    MONDAY(PayType.WEEKDAY),
    TUESDAY(PayType.WEEKDAY),
    WEDNESDAY(PayType.WEEKDAY),
    THURSDAY(PayType.WEEKDAY),
    FRIDAY(PayType.WEEKDAY),
    SATURDAY(PayType.WEEKEND),
    SUNDAY(PayType.WEEKEND);
    
    private final PayType payType;

    PayrollDay(PayType payType) {
        this.payType = payType;
    }

    int pay(int minutesWorked, int payRate) {
        return payType.pay(minutesWorked,payRate);
    }

    private enum PayType {
        WEEKDAY {
            int overtimePay(int minutesWorked, int payRate) {
                return minutesWorked <= MINS_PER_SHIFT ?
                        0 : (minutesWorked - MINS_PER_SHIFT) * payRate / 2;
            }
        },
        WEEKEND {
            int overtimePay(int minutesWorked, int payRate) {
                return minutesWorked * payRate / 2;
            }
        };

        abstract int overtimePay(int minutesWorked, int payRate);
        private static final int MINS_PER_SHIFT = 8 * 60;

        int pay(int minutesWorked, int payRate) {
            int basePay = minutesWorked * payRate;
            return basePay + overtimePay(minutesWorked,payRate);
        }
    }
```
### switch문의 열거타입
+ 기존 열거타입에 상수별 동작을 혼합해 넣을 때 사용
```java
public static Operation inverse(Operation operation) {
        switch (operation) {
            case PLUS:
                return Operation.MINUS;
            case MINUS:
                return Operation.PLUS;
            case TIMES:
                return Operation.DIVDE;
            case DIVDE:
                return Operation.TIMES;
        }
        throw new AssertionError("알 수 없는 연산 : " +operation);
}
```
### 💡 결론
##### 열거타입은 필요한 원소를 컴파일 타임에 다 알 수 있는 상수집합이라면 항상 열거타입을 사용하자.
+ 정의된 상수 개수가 영원히 고정될 필요는 없다.










