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

<br><hr><br>

## 아이템 35. ordinal 메서드 대신 인스턴스 필드를 사용하라.
### ordinal 메서드
+ 열거 타입에서 해당 상수가 몇 번째 위치인지를 반환하는 메서드를 제공
> Returns the ordinal of this enumeration constant (its position in its enum declaration, where the initial constant is assigned an ordinal of zero). Most programmers will have no use for this method. It is designed for use by sophisticated enum-based data structures, such as EnumSet and EnumMap.
### 💡 결론
##### 열거타입 상수에 연결된 값은 ordinal 메서드로 얻지 말고, 인스턴스 필드에 저장하자.
+ 열거타입의 선언 순서에 따라 ordinal 값이 바뀐다.
```java
public enum Ensemble {
  SOLO(1),DUET(2),TRIO(3),QUARTET(4),QUINTENT(5),SEXTET(6),SEPTET(7),OCTET(8),
  DOUBLE_QUARTET(8),NONET(9),DECTET(10),TRIPLE_QUARTET(12);
  
  private final int numberOfMusicians;
  Ensemble(int size){this.numberOfMusicians=size;}
  public int numberOfMusicians(){return numberOfMusicians;}
}
```

<br><hr><br>

## 아이템 36. 비트 필드 대신 EnumSet을 사용하라. 💡 결론
### 비트 필드 열거 상수의 단점
```java
text.applyStyles(STYLE_BOLD | STYLE_UNDERLINE){...};
```
+ 합집합과 교집합 같은 집한 연산 수행을 효율적으로 할 수 있다.
+ 정수 열거 상수의 단점을 그대로 지닌다.
+ API 작성시 사용할 비트 크기를 적절히 예측할 수 있어야 한다.
### EnumSet
+ Set 인터페이스를 구현, Set 구현체와도 함께 사용할 수 있다.
```java
public class Text {
  public enum Style {BOLD, ITALIC, UNDERLINE, STRIKETHROUGH}
  public void applyStyle (Set<Style> styles){...}
}
```
```java
text.applyStyles(EnumSet.of(Style.BOLD, Style.ITALIC));
```
+ 모든 클라이언트가 EnumSet을 건네리라 짐작되는 상황이라도 이왕이면 인터페이스로 받는 게 일반적으로 좋은 습관이다. (아이템 64)
+ 불변 EnumSet을 만들 수 없다는 단점이 있다
  + [Collections.unmodifiableSet](https://stackoverflow.com/questions/2420096/what-does-collections-unmodifiableset-do-in-java)
  ```java
  private Set<String> words = new HashSet<>(Arrays.asList("existing word"));
  // can
  String word = words.iterator().next();
  // can't
  words = new HashSet<>(); // compilation error
  words.add("new word"); // runtime error UnsupportedOperationException
  ```
  
<br><hr><br>

## 아이템 37. ordinal 인덱싱 대신 EnumMap을 사용하라.
```java
public class Plant {
    enum LifeCycle { ANNUAL, PERNNIAL, BIENNIAL }
  
    final String name;
    final LifeCycle lifeCycle;

    public Plant(String name, LifeCycle lifeCycle) {
        this.name = name;
        this.lifeCycle = lifeCycle;
    }

    @Override
    public String toString() {
        return name;
    }
}
```
```java
public static void usingOrdinalArray(List<Plant> garden) {
    Set<Plant>[] plantsByLifeCycle = (Set<Plant>[]) new Set[LifeCycle.values().length];
    for (int i = 0 ; i < plantsByLifeCycle.length ; i++) {
        plantsByLifeCycle[i] = new HashSet<>();
    }

    for (Plant plant : garden) {
        plantsByLifeCycle[plant.lifeCycle.ordinal()].add(plant);
    }

    for (int i = 0 ; i < plantsByLifeCycle.length ; i++) {
        System.out.printf("%s : %s%n",
                LifeCycle.values()[i], plantsByLifeCycle[i]);
    }
}
```
### ordinal 인덱싱 사용의 문제점
1. 배열은 제네릭과 호환되지 않는다. 컴파일이 깔끔하지 않다.
2. 배열은 각 인덱스의 의미를 모르니 레이블을 직접 달아야 한다.
3. 정확한 정숫값을 사용한다는 것을 직접 보증해야 한다.
### EnumMap
```java
public static void usingEnumMap(List<Plant> garden) {
Map<LifeCycle, Set<Plant>> plantsByLifeCycle = new EnumMap<>(LifeCycle.class);

for (LifeCycle lc : LifeCycle.values()) {
    plantsByLifeCycle.put(lc,new HashSet<>());
}

for (Plant p : garden) {
    plantsByLifeCycle.get(p.lifeCycle).add(p);
}

System.out.println(plantsByLifeCycle);
```

<br><hr><br>

## 아이템 38. 확장할 수 있는 열거 타입이 필요하면 인터페이스를 사용하라
### 열거타입과 확장성
+ 대부분의 경우에서 열거타입을 확장하는 것은 좋지 않은 생각이다.
  + 기반 타입과 확장된 타이들의 원소를 모두 순회할 방법이 마땅치 않다.
  + 확장성을 높이기 위한 설계와 구현이 더 복잡하다
### 확장 가능 열거타입
```java
public interface Operation {
  double apply (double x, double y);
}
```
```java
public enum ExtendedOperation implements Operation {
    EXP("^") {
        @Override
        public double apply(double x, double y) {
            return Math.pow(x, y);
        }
    },
    REMAINDER("%") {
        @Override
        public double apply(double x, double y) {
            return x % y;
        }
    };

    private final String symbol;

    ExtendedOperation(String symbol) {
        this.symbol = symbol;
    }

    @Override
    public String toString() {
        return symbol;
    }
}
```

<br><hr><br>

## 아이템 39. 명명 패턴보다는 애너테이션을 사용하라.
### 명명패턴의 단점
1. 실수로 이름을 작성한 테스트 메서드를 성공으로 넘길 수 있다. (JUnit3)
2. 올바른 프로그램 요소에서만 사용되리라 보장할 수 없다.
   + 클래스 이름에 명명한 JUnit 테스트는 수행되지 않는다.
3. 프로그램 요소를 매개변수로 전달할 마땅한 방법이 없다.
### 애너테이션
##### 마커 애너테이션
```java
/**
* 테스트 메서드임을 선언하는 애너테이션
* 매개변수 없는 정적 메서드 전용
*/
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Test{}
```
+ @Retention과 @Target은 매타에너테이션으로 애너테이션 선언에 다는 애너테이션

##### 반복 가능 애너테이션
```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@Repeatable(ExceptionTestContainer.class)
public @interface ExceptionTest {
    Class<? extends Throwable> value();
}
```

<br><hr><br>

## 아이템 40. @Override 애너테이션을 일관되게 사용하라.
+ 재정의하려는 메서드에 사용하므로서 컴파일 에러로 문제가 있다면 곧장 수정할 수 있다.
+ 구체 클래스에서 상위 클래스의 추상메서드를 재정의하는 경우는 @Override를 달지 않아도 컴파일러가 알려준다.
+ 추상 클래스나 인터페이스에서는 상위 클래스나 상위 인터페이스의 매서드를 재정의하는 모든 메서드에 @Override를 달아주자.

<br><hr><br>

## 아이템 41. 정의하려는 것이 타입이라면 마커 인터페이스를 사용하라.
### 마커 인터페이스
+ 아무 메서드도 담고 있지 않고, 단지 자신을 구현하는 클래스가 특정 속성을 가짐을 표현해주는 인터페이스
+ Serializable 인터페이스
##### 마커 애너테이션보다 좋은 이유
1. 마커 인터페이스는 이를 구현한 클래스의 인스턴스를 구분하는 타입으로 쓸 수 있다.
   + 컴파일시 발생 오류를 잡을 수 있다.
2. 적용 대상을 더 정밀하게 지정할 수 있다.
   + 애너테이션은 @Target 을 이용해서 클래스, 인터페이스, 열거 타입, 애너테이션에 부착 할 수 있기 때문에 세밀하게 제한하기가 어렵다.
   + 마킹하고 싶은 클래스에서만 마커 인터페이스를 사용하므로서 타입을 보장한다.
###### Set을 마커 인터페이스로 생각하지 않는 이유
+ Collection의 메서드 규약을 몇 개 수정했기 때문
### 💡 결론
##### 마커 애너테이션을 사용이 필요한 경우
+ 클래스와 인터페이스 외의 프로그램 요소(모듈, 패키지, 필드, 지역변수)에 마킹을 해야한다
+ 애너테이션을 활발히 사용하는 프레임워크에서의 마커
##### 마커 인터페이스를 사용이 필요한 경우
+ 마킹된 객체를 매개변수로 받는 메서드를 작성할 일이 있다면 마커 인터페이스를 써야한다. 
   + 컴파일 에러를 잡아낼 수 있다.