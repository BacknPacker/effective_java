# Effective Java 5장 열거 타입과 애너테이션



## Item 34. int 상수 대신 열거 타입을 사용하라.



### 열거 타입

일정 개수의 상수를 정의한 다음 그 외의 값은 허용하지 않는 타입



#### 정수 열거 패턴의 단점

##### 타입 안전을 보장할 방법이 없고 표현력이 좋지 않다.

* 메소드에 잘못된 값을 보내더라도 컴파일러는 경고 메세지를 출력하지 않는다.



##### 정수 열거 패턴을 사용하는 프로그램은 깨지기 쉽다.

* 컴파일하면 그 값이 그대로 클라이언트 파일에 새겨지기 때문에 값이 바뀌는 경우 재컴파일을 해야 한다.



##### 정수 상수는 문자열로 출력하기 다소 까다롭다.

* 출력 또는 디버거로 확인해보면 의미가 아닌 숫자로 보여서 도움이 되지 않는다.
* 문자열 열거 패턴이라는 변형을 사용하여 상수의 의미를 출력할 수는 있지만 문자열 값 그대로 하드코딩해야 하는 와중에 휴먼에러를 통한 오타를 찾기 어렵다.



이러한 열거 패턴의 단점을 없애기 위해 **열거 타입**을 제시했다.

```java
public enum Apple{
    FUJI, PIPPIN, GRANNY_SMITH
}
public enum Orange{
    NAVEL, TEMPLE, BLOOD
}
```



#### 열거 타입의 특징

* 열거 타입 자체는 클래스이다. 
* 상수 하나당 자신의 인스턴스를 하나씩 만들어 public static final 필드로 공개한다. 
  * 밖에서 접근할 수 있는 생성자를 제공하지 않기에 사실상 final이다. 
* 열거 타입 선언으로 만들어진 인스턴스들은 딱 하나씩만 존재함이 보장된다.(인스턴스 통제)
  * 싱글톤은 원소가 하나인 열거 타입이라 볼 수 있고 열거 타입은 싱글턴의 일반화이다.
* 타입 안전성을 제공한다. 
* `namespace`가 있어서 이름이 같은 상수도 공존할 수 있다. 
* 임의의 메소드나 필드를 추가할 수 있고, 임의의 인터페이스를 구현하게 할 수도 있다. 
* 열거 타입은 자신 안의 정의된 상수들의 값을 배열에 담아 반환하는 정적메소드(values)를 제공한다. 



#### 열거 타입의 사용 방안

##### 각 상수와 연관된 데이터를 상수 자체에 내재시키고 싶을 때 사용

* 실제로는 클래스이기 때문에 어떠한 메소드도 추가 가능하다.

```java
public enum Planet {
    MERCURY(3.302e+23, 2.439e6),
    VENUS(4.869e+24, 6.052e6),
    EARTH(5.975e+24, 6.378e6),
    MARS(6.419e+23, 3.393e6),
    JUPITER(1.899e+27, 7.149e7),
    SATURN(5.685e+26, 6.027e7),
    URANUS(8.683e+25, 0.556e7),
    NEPTUNE(1.024e+26, 2.477e7);

    private final double mass;              //질량(kg)
    private final double radius;            //반지름(meter)
    private final double surfaceGravity;    //표면중력(m / s^2)

    private static final double G = 6.67300E-11;	//중력상수(m^3 / kg s^2)

    Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
        surfaceGravity = G * mass / (radius * radius);
    }

    public double mass() { return mass; }
    public double radius() { return radius; }
    public double surfaceGravity() { return surfaceGravity; }

    public double surfaceWeight(double mass) {
        return mass * surfaceGravity; // F = ma
    }
}
```

* 열거 타입 상수 각각을 특정 데이터와 연결지으려면 생성자에서 데이터를 받아 인스턴스 필드에 저장하면 된다.
  * 근본적으로 열거타입이 불변이므로 모든 필드는 `final`이어야 한다.
* 자신 안에 정의된 상수 값들을 배열에 담아 반환하는 정적 메소드인 `values`를 제공한다.
  * 각 열거 타입 값의 toString가 이름을 문자열로 반환하기 때문에 출력하기 좋다.



###### 정수 열거 패턴에서 문제가 되었던 하나의 상수를 제거 하는 것도 열거 타입에서는 클라이언트에 영향이 없다.

* 재컴파일시에 제거된 상수를 참조하는 줄에서 컴파일 오류가 발생해 예외처리하기 때문에 바람직한 대응이 가능하다.



##### 열거 타입을 선언한 클래스 혹은 그 패키지에서만 유용한 기능은 `private` 또는 `package-private` 메소드로 구현한다.

이렇게 구현하면 자신을 선언한 클래스 또는 패키지에서만 사용할 수 있는 기능을 담게 된다.

* 클라이언트에 노출해야 할 이유가 없으면 `private`으로 선언하고, 필요하다면 `package-private`으로 선언하는게 좋다.

널리 쓰인다면 톱레벨 클래스로 만들고, 특정 톱레벨 클래스에서만 쓰인다면 해당 클래스의 멤버 클래스로 만든다



열거 타입은 상수별로 다르게 동작하기 위해 apply라는 추상 메소드를 선언하고 자신에 맞게 재정의하는 클래스 몸체를 이용하는 `상수별 메소드 구현`을 지원한다.

```java
public enum Operation {
    PLUS {
        public double apply(double x, double y) {
            return x + y;
        }
    },
    MINUS {
        public double apply(double x, double y) {
            return x - y;
        }
    },
    TIMES {
        public double apply(double x, double y) {
            return x * y;
        }
    },
    DIVIDE {
        public double apply(double x, double y) {
            return x / y;
        }
    };

    public abstract double apply(double x, double y);
}
```

apply 메소드가 추상 메소드로 선언되어 바로 옆에 붙어있기 때문에 새로운 상수를 추가할 때 apply를 재정의하지 않으면 컴파일되지 않는다. 

이 방법으로 상수별 데이터와 혼용할수도 있다.

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
    DIVIDE("/") {
        public double apply(double x, double y) {
            return x / y;
        }
    };
    public final String symbol;

    Operation(String symbol) {
        this.symbol = symbol;
    }

    public abstract double apply(double x, double y);

    @Override
    public String toString() {
        return symbol;
    }
}
```

이러한 상수별 메소드의 경우 열거 타입 상수끼리 코드 공유가 어렵다. 관리 관점에서 여러 코드에서 사용하는 경우에는 모든 부분에 같은 처리를 해주어야 하기 때문이다. 이 경우 공통으로 사용되는 **코드를  모든 상수에 중복해서 넣는 방법**이나 **도우미 메소드로 작성 후 상수가 필요한 메소드를 호출하는 방식**으로 해결이 가능하다. 하지만 **코드가 장황해서 가독성이 떨어지고, 오류 발생 가능성이 높아진다.** 

이러한 경우 `전략`을 선택하도록 하면 된다. 책에 나온 예시로 예를 들면 잔업 수당 계산을 `private 중첩 열거 타입`으로 옮기고, PayrollDay 열거타입의 생성자에서 적당한 것을 선택하도록 하는 것이다.



```java
public enum PayrollDay {
    MONDAY(WEEKDAY), TUESDAY(WEEKDAY), 
		WEDNESDAY(WEEKDAY), THURSDAY(WEEKDAY), FRIDAY(WEEKDAY),
    SATURDAY(WEEKEND), SUNDAY(WEEKEND);

    private final PayType payType;

    PayrollDay(PayType payType) {
        this.payType = payType;
    }

    int pay(int minutesWorkd, int payRate) {
        return payType.pay(minutesWorkd, payRate);
    }

    enum PayType {
        WEEKDAY {
            @Override
            int overtimePay(int mins, int payRate) {
                return minWorked <= MINS_PER_SHIFT ? 0 : 
                	(minWorked - MINS_PER_SHIFT) * payRate / 2;
            }
        },
        WEEKEND {
            @Override
            int overtimePay(int mins, int payRate) {
                return minsWorked * payRate / 2;
            }
        };

        abstract int overtimePay(int mins, int payRate);
        private static final int MINS_PER_SHIFT = 8 * 60;

        int pay(int minsWorked, int payRate) {
            int basePay = minsWorked * payRate;
            return basePay + overtimePay(minsWorked, payRate);
        }
    }
}
```

이 방식이 switch문을 사용하는 것 보다 복잡하지만 더 **안전하고 유연한 코드를 제공**한다. 또한 switch문의 경우 열거 타입의 상수별 동작을 구현하는데는 적합하지 않기 때문에 **기존 열거타입에 상수별 동작을 혼합하는 경우에는 switch문이 더 좋은 선택이 될 수 있다.**



#### 열거 타입을 사용하는 경우

필요한 원소를 컴파일 타임에 다 알 수 있는 상수 집합이라면 사용하면 된다.

열거 타입은 상수 개수가 바이너리 수준에서 호환되도록 설계되었기 때문에 **영원히 고정 불변일 필요는 없다.**



---



## Item 35. ordinal 메소드 대신 인스턴스 필드를 사용하라.

`ordinal 메소드`는 찾고자 하는 상수가 열거 타입에서 몇 번째 위치인지를 반환하는 메소드이다. 유용하게 사용이 가능하지만 **상수 선언 순서를 바꾸는 순간 메소드가 오동작하게된다.** 또한 **값의 중간을 비워둘 수 없다**라는 단점도 존재한다.

이 문제에 대한 해결방안은 **열거 타입 상수에 연결된 값은 ordinal 메소드로 얻지 말고, 인스턴스 필드에 저장**하면된다.

Enum API 문서에 아래와 같이 명시되어있다.

![1652192193358](ddamdoo_내용요약.assets/1652192193358.png)

> 대부분 프로그래머는 이 메소드를 쓸 일이 없다. 이 메소드는 EnumSet과 EnumMap 같이 열거 타입 기반의 범용 자료구조에 쓸 목적으로 설계되었다.
>
> https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/Enum.html



---



## Item 36. 비트 대신 EnumSet을 사용하라

열거한 값들이 집합으로 사용되는 경우 각 상수에 서로 다른 2의 거듭제곱 값을 할당한 정수 열거 패턴을 사용해왔다.

```java
public class Text {
    public static final int STYLE_BOLD          = 1 << 0; //1
    public static final int STYLE_ITALIC        = 1 << 1; //2
    public static final int STYLE_UNDERLINE     = 1 << 2; //4
    public static final int STYLE_STRIKETHROUGH = 1 << 3; //8

    public void applyStyles(int styles) {
    }
}
```

이 값들은 비트별 OR을 사용해 여러 상수를 하나의 집합으로 모을 수 있고 이러한 집합을 `비트 필드`라고 한다.

비트 필드를 사용하면 비트별 연산을 통해 **집합 연산을 효율적으로 수행할 수 있다**는 장점이 있지만, **정수 열거 상수의 단점**과 **정수 열거 타입보다 해석이 어렵다**는 단점이 있다. 또한 **최대 몇 비트가 필요한지를 API작성 시 미리 예층하여 적절한 타입을 선택해야 한다.**

따라서 이에 대한 대안으로 java.util 패키지의 `EnumSet` 클래스는 열거 타입 상수의 값으로 구성된 집합을 효과적으로 표현해준다. 

* Set 인터페이스를 완벽히 구현하고, 타입이 안전하며 다른 어떤 Set 구현체와도 함께 사용 가능하다.
* 내부가 비트 벡터로 구현되어 있으며 원소가 64개 이하인 경우 EnumSet 전체를 대부분 long 변수 하나로 표현하여 비트 필드에 비견되는 성능을 보여준다. 

* `removeAll`과 `retainAll`과 같은 대량 작업은 비트를 효율적으로 처리할 수 있는 산술 연산을 써서 구현했다. 
  * 비트를 직접 다룰 때 겪는 문제들을 EnumSet에서 대부분 해결해준다. 

```java
public class Text {  
    public enum Style { BOLD, ITALIC, UNDERLINE, STRIKETHROUGH }
    public void applyStyles(Set<Style> styleSet){ ... }
}
```



---



## Item 37. ordinal 인덱싱 대신 EnumMap을 사용하라



배열이나 리스트에서 원소를 꺼낼 때 ordinal 메소드를 통해서 인덱스를 가져오는 경우가 있는데 여기에는 문제점이 존재한다.

* 배열과 제네릭이 호환되지 않아 비검사 형변환을 수행해야하고, 컴파일이 깔끔하게 되지 않는다.
* 배열은 각 인덱스의 의미를 모르기에 출력 결과에 직접 레이블을 달아야 한다. 
* 정수는 열거 타입과 다르게 타입 안전하지 않기 때문에 정확한 정수값을 사용한다는 것을 개발자가 직접 보증해야 한다. 



여기서 배열은 실질적으로 열거 타입 상수를 값으로 매핑하는 일을 하기에 `Map`을 사용할 수 있고, 열거 타입을 키로 사용하도록 빠른 map 구현체인 `EnumMap`을 사용하면 된다.



```java
public class Plant {
    enum LifeCycle { ANNUAL, PERENNIAL, BIENNIAL }

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
Map<Plant.LifeCycle, Set<Plant>> plantByLifeCycle = new EnumMap<>(Plant.LifeCycle.class);

for (Plant.LifeCycle lc : Plant.LifeCycle.values()) {
    plantByLifeCycle.put(lc, new HashSet<>());
}

for (Plant p : garden) {
    plantByLifeCycle.get(p.lifeCycle).add(p);
}

System.out.println(plantByLifeCycle);
```

위의 기본 코드를 EnumMap으로 리팩토링한 코드이다. 

리팩토링으로 인해 코드가 더 짧고 깔끔해졌으며 더하여 Map을 사용했기에 타입 안정적이고 성능마저 빠르다. 

* 성능이 빠른 이유는 내부적으로 배열을 사용하기 때문이다.
  * 이러한 구현을 내부로 숨겨 Map의 타입 안전성과 배열의 성능을 모두 얻어냈다. 



자바 8 이후에는 스트림을 통해서 맵을 관리하여 코드를 더 가독성 좋게 할 수 있다. 단순한 프로그램이라면 상관없지만 맵을 자주 사용할수록 이런 리팩토링은 필수다. 

```java
System.out.println(Arrays.stream(garden)
          .collect(Collectors.groupingBy(p -> p.lifeCycle, 
                  () -> new EnumMap<>(Plant.LifeCycle.class), toSet())));
```

주의사항은 위의 스트림을 이용한 코드는 `EnumMap`만 사용할 경우와는 다르게 동작한다는 것이다.

* 스트림을 이용한 방식은 내용이 없는 카테고리 즉, **열거타입의 상수에 대해서는 값을 만들지 않는다.**

열거타입 LifeCycle에 3개의 상수가 있다고 할 때, 만약 이중 2개에 해당하는 식물들만 있다면 결과로 나온 `EnumMap`에는 이 두 가지 그룹에 대해서만 만든다. 



#### 두 열거 타입의 값들을 매핑하느라 ordinal을 (두 번이나) 쓴 배열들의 배열

이는 두 개의 열거 타입을 억지로 매핑하기 위해 ordinal을 두 번이나 쓴 잘못된 방법이다.

##### 문제점

- 컴파일러가 ordinal과 배열 인덱스의 관계를 알 수 없다.
  - Phase나 Phase.Transition 열거 타입을 수정하면서 표 TRANSITIONS 를 함께 수정하지 않거나 실수로 잘못 수정하면 런타임 오류가 발생할 것이다.
- `ArrayIndexOutOfBoundsException` 이나 `NullPointerException` 을 던질 수도 있고, 예외없이 의도하지 않도록 동작할 수 있다.
- 표의 크기는 상태의 가지수가 늘어나면 제곱해서 커지며, null로 채워지는 칸도 늘어날 것이다.

```java
public enum Phase {
    SOLID,
    LIQUID,
    GAS;

    public enum Transition {
        MELT,
        FREEZE,
        BOIL,
        CONDENSE,
        SUBLIME,
        DEPOSIT;

        private static final Transition[][] TRANSITIONS = {
                {null, MELT, SUBLIME},
                {FREEZE, null, BOIL},
                {DEPOSIT, CONDENSE, null}
        };

        public static Transition from(Phase from, Phase to) {
            return TRANSITIONS[from.ordinal()][to.ordinal()];
        }
    }
}
```

이 코드 역시 EnumMap을 이용하여 리팩토링이 가능하다.

전이 하나를 얻기 위해 이전 상태(from)와 이후 상태(to)가 필요하기 때문에 Map 2개를 중첩하여 쉽게 해결할 수 있다.

- 안쪽 Map은 이전 상태와 TRANSITION 을 연결
- 바깥 Map은 이후 상태와 안쪽 Map을 연결
- OuterMap -> 이후 상태 & InnerMap -> 이전 상태 & TRANSITION
- 전이 전후의 두 상태를 전이 열거 타입 Transition의 입력으로 받아, 이 Transition 상수들로 중첩된 EnumMap을 초기화 한다.

```java
public enum Phase {
    SOLID, LIQUID, GAS;

    public enum Transition {
        MELT(SOLID, LIQUID), FREEZE(LIQUID, SOLID),
        BOIL(LIQUID, GAS), CONDENSE(GAS, LIQUID),
        SUBLIME(SOLID, GAS), DEPOSIT(GAS, SOLID);

        private final Phase from;
        private final Phase to;

        Transition(Phase from, Phase to) {
            this.from = from;
            this.to = to;
        }

        private static final Map<Phase, Map<Phase, Transition>> m = Stream.of(values())
            .collect(groupingBy(t -> t.from,() -> new EnumMap<>(Phase.class),
                                toMap(t -> t.to, t -> t, (x, y) -> y, () -> new EnumMap<>(Phase.class))));

        public static Transition from(Phase from, Phase to) {
            return m.get(from).get(to);
        }
    }
}
```

여기서 `Map<Phase, Map<Phase, Transition>>` 은 **"이전 상태에서 '이후 상태에서 전이로의 Map' 에 대응시키는 Map"**이라는 뜻이다.

이러한 Map의 Map을 초기화하기 위해 수집기(`java.util.stream.Collector`) 2개를 차례로 사용되었다.

- 첫 번째 수집기인 groupingBy에서는 전이를 이전 상태를 기준으로 묶었다.
- 두 번째 수집기인 toMap 에서는 이후 상태를 전이에 대응시키는 EnumMap을 생성한다.
  - 두 번째 수집기의 병합 함수인 (x, y) -> y는 선언만 하고 실제로는 쓰이지 않는다.
  - 이는 단지 EnumMap을 얻으려면 MapFactory가 필요하고 수집기들은 점층적 팩토리(telescoping factory)를 제공하기 때문이다.

#### 새로운 상태를 추가하는 경우

새로운 상태인 플라즈마(PLASMA) 추가하고 연결된 전이를 생성한다.

- 기체에서 플라즈마로 변하는 이온화(IONIZE)
- 플라즈마에서 기체로 변하는 탈이온화(DEIONIZE)

그 이후 상태 목록에 `PLASMA`를 추가하고, 전이 목록에 `IONIZE(GAS, PLASMA)`와 `DEIONIZE(PLASMA, GAS)`만 추가하면 끝이다.

```java
public enum Phase {
    SOLID, LIQUID, GAS, PLASMA;

    public enum Transition {
        MELT(SOLID, LIQUID), FREEZE(LIQUID, SOLID),
        BOIL(LIQUID, GAS), CONDENSE(GAS, LIQUID),
        SUBLIME(SOLID, GAS), DEPOSIT(GAS, SOLID),
        // IONIZE, DEIONIZE 추가
        IONIZE(GAS, PLASMA), DEIONIZE(PLASMA, GAS);
        
        ...
```

enum 두 개를 사용하여 데이터를 조합하여 사용하는 경우 2차원 배열을 사용하는 것보다 EnumMap을 사용하는 것이 **Collectors.groupingBy와 EnumMap의 조합으로 조회가 편리해지고, 성능 면에서도 이점**이 있고, 실제 **내부에서는 Map의 Map이 배열의 배열로 구현되어 낭비되는 공간과 시간도 거의 없이 명확하고 안전**하고 유지보수에 좋다.



---



## Item 38. 확장할 수 있는 열거 타임이 필요하면 인터페이스를 사용하라

열거 타입은 대부분의 상황에서 타입 안전 열거 패턴보다 우수하다. 하지만 타입 안전 열거 패턴은 확장이 가능하지만 **열거 타입은 확장이 불가능하다.**

대부분 상황에서 열거 타입을 확장하려는 시도가 좋은 생각은 아니다. 확장 타입 원소는 기반 타입 원소로 취급되지만 반대의 경우는 성립하지도 않고, 기반타입과 확장타입 원소를 모두 순회할 방법도 없다. 그리고 확장성을 높히려고 할 때 고려할 설계와 구현이 너무 복잡해진다. 

하지만 연산코드를 확장할 수 있는 열거타입이 어울린다. 연산 코드의 각 원소는 특정 기계가 수행하는 연산을 의미하는데 이따금 API가 제공하는 기본 연산 외에 사용자 확장 연산을 추가할 수 있도록 해야 하는 경우가 있다.

이 때 열거 타입은 **인터페이스를 구현해서 확장한다.** 연산 코드용 인터페이스를 정의한 뒤 열거 타입에서 이 인터페이스를 구현하게 하면 된다.

```java
public interface Operation {
    double apply(double x, double y);
}
```

이러한 인터페이스를 작성한 후, 아래에 기본 연산을 정의해준다.

```java
public enum BasicOperation implements Operation {
    PLUS("+") {
        @Override
        public double apply(double x, double y) { return x + y; }
    },
    MINUS("-") {
        @Override
        public double apply(double x, double y) { return x - y; }
    },
    TIMES("*") {
        @Override
        public double apply(double x, double y) { return x * y; }
    },
    DIVIDE("/") {
        @Override
        public double apply(double x, double y) { return x / y; }
    };

    private final String symbol;

    BasicOperation(String symbol) {
        this.symbol = symbol;
    }

    @Override
    public String toString() { return symbol; }
}
```

그 이후 추가적으로 확장이 필요한 코드를 작성하면 된다.

```java
public enum ExtendedOperation implements Operation {
    EXP("^") {
        @Override
        public double apply(double x, double y) { return Math.pow(x, y); }
    },
    REMAINDER("%") {
        @Override
        public double apply(double x, double y) { return x % y; }
    };

    private final String symbol;

    ExtendedOperation(String symbol) {
        this.symbol = symbol;
    }
    
    @Override
    public String toString() { return symbol; }
}
```

이렇게 구현한 인터페이스와 열거 타입은 이제 열거타입에서 따로 추상 메서드를 선언하지 않아도 된다. 개별 인스턴스 수준에서 뿐만 아니라 타입 수준에서도 기본 열거 타입 대신 확장된 열거 타입을 넘겨 확장된 열거 타입의 원소 모두를 사용하게 할 수도 있다. 이러한 방식을 이용하는 것은 크게 두 가지가 있다.

#### 한정적 타입 토큰

```java
public static void main(String[] args) {
    double x = Double.parseDouble(args[0]);
    double y = Double.parseDouble(args[1]);
    test(ExtendedOperation.class, x, y);
}

private static <T extends Enum<T> & Operation> void test(
    Class<T> opEnumType, double x, double y) {
    for (Operation op : opEnumType.getEnumConstants()) {
        System.out.printf("%f %s %f = %f%n", x, op, y, op.apply(x, y));
    }
}
```

이 코드는 test 메서드에 인수로 ExtendedOperation의 class 리터럴을 넘겨 이를 이용한 방법이다. 

`<T extends Enum<T> & Operation>` 매개변수의 선언은 Class 객체가 열거타입인 동시에 Operation 인터페이스의 구현체여야 한다는 의미다. 



#### 한정적 와일드카드 타입

```java
public static void main(String[] args) {
    double x = 4, y = 2;
    testV2(Arrays.asList(ExtendedOperation.values()), x, y);
}

private static void test(Collection<? extends Operation> opSet, double x, double y) {
    for (Operation op : opSet) {
        System.out.printf("%f %s %f = %f%n", x, op, y, op.apply(x, y));
    }
}
```

인수로 `Collection<? extends Operation>` 타입의 한정적 와일드카드 타입을 넘기는 방식이다. 한정적 타입 토큰 방식에 비해 코드가 덜 복잡하고  test 메소드도 더 유연해졌다. 하지만 **특정 연산에서 EnumMap이나 EnumSet을 사용할 수 없다**는 단점이 있다.



인터페이스를 이용해 확장 가능한 열거 타입을 내는 방식도 **열거 타입끼리는 구현을 상속할 수 없다**는 문제가 존재한다. 아무 상태에도 의존하지 않으면 디폴트 구현을 이용하여 인터페이스에 추가하는 방법도 있다. 위 예시들처럼 중복량이 적은 코드는 상관없지만 공유하는 기능이 많다면 별도의 도우미 클래스나 정적 도우미 메소드로 분리해서 코드 중복을 없애주어야 한다.

