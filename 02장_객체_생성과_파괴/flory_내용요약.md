# 2장. 객체 생성과 파괴

## ✨ 아이템1. 생성자 대신 정적 팩터리 메서드를 고려하라.
***
클래스는 생성자와 별도로 정적 팩터리 메서드를 제공할 수 있다.

디자인 패턴의 팩터리 메서드와 무관하다.
``` java
public static Boolean valueOf(boolean b){
  return b ? Boolean.TRUE : Bloolean.FALSE;
}
```
### ✔️ 정적 팩터리 메서드의 장점
#### 1. 반환 객체 특성을 제대로 묘사하는 이름을 지을 수 있다.
+ BigInteger(int,int,Random) vs BigInteger.probablePrime
+ 동일한 시그니처를 가지는 메서드를 만들고자 할 때


#### 2. 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.
+ 생성 비용이 큰 객체가 자주 요청되는 상황 (feat. Flyweight pattern)
+ 언제 어느 인스턴스를 살아 있게 할지 철저히 통제할 수 있다. (인스턴스 통제 클래스)

    
#### 3. 반환 타입의 하위 타입 객체를 반환할 수 있다.
+ 구현 클래스를 공개하지 않고도 그 객체를 반환할 수 있어서 API를 작게 유지 가능.

    
#### 4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.
+ 반환 타입의 하위 타입이기만 하면 어떤 클래스의 객체를 반환하든 상관없다.

    
#### 5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.
+ 서비스 제공자 프레임워크를 만드는 근간이 된다. (ex:JDBC)
    ```
   💡 서비스 제공자 프레임워크를 이루는 3개의 핵심 컴포넌트
   
       서비스 인터페이스 : 구현체의 동작을 정의
       제공자 등록 API : 제공자가 구현체를 등록할 때 사용
       서비스 접근 API : 클라이언트가 서비스의 인스턴스를 얻을 때 사용
   ```
  
### ✔️ 정적 팩터리 메서드의 단점
#### 1. 하위 클래스를 만들 수 없다.
+ 상속을 하려면 public 이나 protected 생성자가 필요하기 때문이다.
#### 2. 프로그래머가 찾기 어렵다.
+ 생성자처럼 API설명에 명확히 드러나지 않는다.

``` 
🎨 알아야하는 디자인패턴

✔️ Factory Mothod pattern
✔️ Flyweight pattern
✔️ Bridge pattern
```



## ✨ 아이템2. 생성자에 매개변수가 많다면 빌더를 고려하라.
***
정적 팩터리와 생성자는 선택적 매개변수가 많을때 적절히 대응하기 어렵다.

그래서 우리는 점층적 생성자 패턴, 자바빈즈 패턴, 빌더 패턴을 알아보며 왜 빌더를 고려해야 하는지 알아보자.

#### 1. 점층적 생성자 패턴 (Telescoping constructor pattern)  
: 필수 매개변수만 받는 생성자, 필수 매개변수와 선택 매개변수 1개를 받는 생성자, 선택 매개변수를 2개까지 받는 생성자, ... 형태로 선택 매개변수를 전부 다 받는 생성자까지 늘려가는 방식.
   ``` java
   public class NutritionFacts {
    private final int servingSize;  // (mL, 1회 제공량)     필수
    private final int servings;     // (회, 총 n회 제공량)  필수
    private final int calories;     // (1회 제공량당)       선택
    private final int fat;          // (g/1회 제공량)       선택
    private final int sodium;       // (mg/1회 제공량)      선택
    private final int carbohydrate; // (g/1회 제공량)       선택

    public NutritionFacts(int servingSize, int servings) {
        this(servingSize, servings, 0);
    }

    public NutritionFacts(int servingSize, int servings,
                          int calories) {
        this(servingSize, servings, calories, 0);
    }

    public NutritionFacts(int servingSize, int servings,
                          int calories, int fat) {
        this(servingSize, servings, calories, fat, 0);
    }

    public NutritionFacts(int servingSize, int servings,
                          int calories, int fat, int sodium) {
        this(servingSize, servings, calories, fat, sodium, 0);
    }
    public NutritionFacts(int servingSize, int servings,
                          int calories, int fat, int sodium, int carbohydrate) {
        this.servingSize  = servingSize;
        this.servings     = servings;
        this.calories     = calories;
        this.fat          = fat;
        this.sodium       = sodium;
        this.carbohydrate = carbohydrate;
    }

    public static void main(String[] args) {
        NutritionFacts cocaCola =
                new NutritionFacts(240, 8, 100, 0, 35, 27);
    }
   }
   ```
   ➡️ 사용자가 설정하길 원치 않는 매개변수까지 값을 지정해줘야 한다.  
   ➡️ 만약 매개변수 개수가 많아진다면, 클라이언트 코드를 작성하거나 읽기 어렵게 된다.


#### 2. 자바빈즈 패턴 (JavaBeans pattern)  
   : 매개변수가 없는 생성자로 객체를 만든 후, Setter 메서드들을 호출해 원하는 매개변수의 값을 설정하는 방식.
   ``` java
   public class NutritionFacts {
    // 매개변수들은 (기본값이 있다면) 기본값으로 초기화된다.
    private int servingSize  = -1; // 필수; 기본값 없음
    private int servings     = -1; // 필수; 기본값 없음
    private int calories     = 0;
    private int fat          = 0;
    private int sodium       = 0;
    private int carbohydrate = 0;

    public NutritionFacts() { }
    // Setters
    public void setServingSize(int val)  { servingSize = val; }
    public void setServings(int val)     { servings = val; }
    public void setCalories(int val)     { calories = val; }
    public void setFat(int val)          { fat = val; }
    public void setSodium(int val)       { sodium = val; }
    public void setCarbohydrate(int val) { carbohydrate = val; }

    public static void main(String[] args) {
        NutritionFacts cocaCola = new NutritionFacts();
        cocaCola.setServingSize(240);
        cocaCola.setServings(8);
        cocaCola.setCalories(100);
        cocaCola.setSodium(35);
        cocaCola.setCarbohydrate(27);
    }
   }
   ```
   ➡️ 점층적 생성자 패턴의 단점은 보이지 않지만 자바빈즈만의 단점이 있다.  
   ➡️ 객체 하나를 만들려면 메서드 여러 개 호출해야 하고, 객체가 완전히 생성되기 전까지는 일관성(consistency)이 무너진 상태에 놓이게 된다.


#### 3. 빌더 패턴 (Builder pattern)  
: 필요한 객체를 직접 만드는 대신, 필수 매개변수만으로 생성자 또는 정적 팩터리 메서드를 호출해 빌더 객체를 얻는다. 그다음 빌더 객체가 제공하는 일종의 Setter 메서드들로 원하는 선택 매개변수들을 설정한다. 마지막으로 매개변수가 없는 build 메서드를 호출해 필요한 객체를 얻는다.  
빌더는 생성할 클래스 안에 정적 멤버 클래스로 만들어 두는게 보통이다.
   ``` java
   public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public static class Builder {
        // 필수 매개변수
        private final int servingSize;
        private final int servings;

        // 선택 매개변수 - 기본값으로 초기화한다.
        private int calories      = 0;
        private int fat           = 0;
        private int sodium        = 0;
        private int carbohydrate  = 0;

        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings    = servings;
        }

        public Builder calories(int val)
        { calories = val;      return this; }
        public Builder fat(int val)
        { fat = val;           return this; }
        public Builder sodium(int val)
        { sodium = val;        return this; }
        public Builder carbohydrate(int val)
        { carbohydrate = val;  return this; }

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }

    private NutritionFacts(Builder builder) {
        servingSize  = builder.servingSize;
        servings     = builder.servings;
        calories     = builder.calories;
        fat          = builder.fat;
        sodium       = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }

    public static void main(String[] args) {
        NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
                .calories(100).sodium(35).carbohydrate(27).build();
    }
   }
   ```
   ➡️ ️빌더의 Setter 메서드들은 빌더 자신을 반환하기 때문에 연쇄적으로 호출할 수 있다.  
   ➡️ 쓰고 읽기가 쉽다.  
   ➡️ 빌더 패턴은 매개변수가 4개 이상은 되어야 값어치를 하지만, API는 시간이 지날수록 매개변수가 많아지는 경향이 있으므로 애초에 빌더로 시작하는 편이 좋다.

#### 빌더 패턴은 계층적으로 설계된 클래스와 함께 쓰기에 좋다.
``` java
// 참고: 여기서 사용한 '시뮬레이트한 셀프 타입(simulated self-type)' 관용구는
// 빌더뿐 아니라 임의의 유동적인 계층구조를 허용한다.

public abstract class Pizza {
    public enum Topping { HAM, MUSHROOM, ONION, PEPPER, SAUSAGE }
    final Set<Topping> toppings;

    abstract static class Builder<T extends Builder<T>> {
        EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
        public T addTopping(Topping topping) {
            toppings.add(Objects.requireNonNull(topping));
            return self();
        }

        abstract Pizza build();

        // 하위 클래스는 이 메서드를 재정의(overriding)하여
        // "this"를 반환하도록 해야 한다.
        protected abstract T self();
    }
    
    Pizza(Builder<?> builder) {
        toppings = builder.toppings.clone(); // 아이템 50 참조
    }
}
```
➡️ Pizza.Builder 클래스에 추상 메서드인 self를 더해 하위 클래스에서는 형변환하지 않고도 메서드 연쇄를 지원할 수 있다.  
➡️ Pizza의 하위 클래스는 NyPizza, Calzone 피자가 있다고 하자.
  

``` java
public class NyPizza extends Pizza {
    public enum Size { SMALL, MEDIUM, LARGE }
    private final Size size;

    public static class Builder extends Pizza.Builder<Builder> {
        private final Size size;

        public Builder(Size size) {
            this.size = Objects.requireNonNull(size);
        }

        @Override public NyPizza build() {
            return new NyPizza(this);
        }

        @Override protected Builder self() { return this; }
    }

    private NyPizza(Builder builder) {
        super(builder);
        size = builder.size;
    }

    @Override public String toString() {
        return toppings + "로 토핑한 뉴욕 피자";
    }
}
```
➡️ size 매개변수를 필수로 받는다.  
➡️ NyPizza.Builder는 NyPizza를 반환


```java
public class Calzone extends Pizza {
    private final boolean sauceInside;

    public static class Builder extends Pizza.Builder<Builder> {
        private boolean sauceInside = false; // 기본값

        public Builder sauceInside() {
            sauceInside = true;
            return this;
        }

        @Override public Calzone build() {
            return new Calzone(this);
        }

        @Override protected Builder self() { return this; }
    }

    private Calzone(Builder builder) {
        super(builder);
        sauceInside = builder.sauceInside;
    }

    @Override public String toString() {
        return String.format("%s로 토핑한 칼초네 피자 (소스는 %s에)",
                toppings, sauceInside ? "안" : "바깥");
    }
}
```
➡️ 소스를 안에 넣을지 선택하는 매개변수를 필수로 받는다.  
➡️ Calzone.Builder는 Calzone을 반환한다.  
➡️ 하위 클래스의 메서드가 상위 클래스의 메서드가 정의한 반환 타입이 아닌, 그 하위 타입을 반환하는 기능을 공변 반환 타이핑이라 한다.  
💡 Spring에서 Builder pattern은 @Builder 하나로 끝나지만 위의 원리를 잘 알아두자. 

## ✨ 아이템3. private 생성자나 열거 타입으로 싱글톤임을 보증하라.
***
``` 
💡 싱글톤(Singleton)이란?

   인스턴스를 오직 하나만 생성할 수 있는 클래스이다. 
```
싱글톤을 만드는 방식은 아래와 같이 보통 두가지 방식으로 나뉜다.
두 방식 모두 생성자는 private으로 감춰두고, 유일한 인스턴스에 접근할 수 있는 수단으로 public static 멤버를 하나 마련해둔다.

#### 1. public static 멤버가 final 필드인 방식
   ```java
   // 코드 3-1 public static final 필드 방식의 싱글턴 (23쪽)
   public class Elvis {
   public static final Elvis INSTANCE = new Elvis();
   
       private Elvis() { }
   
       public void leaveTheBuilding() {
           System.out.println("Whoa baby, I'm outta here!");
       }
   
       // 이 메서드는 보통 클래스 바깥(다른 클래스)에 작성해야 한다!
       public static void main(String[] args) {
           Elvis elvis = Elvis.INSTANCE;
           elvis.leaveTheBuilding();
       }
   }
   ```
   ➡️ private 생성자는 public static final 필드인 Elvis.INSTANCE를 초기화할 때 딱 한 번만 호출된다.  
   ➡️ public이나 protected 생성자가 없으므로 Elvis 클래스가 초기화될 때 만들어진 인스턴스가 전체 시스템에서 하나뿐임이 보장된다.  
   ➡️ 해당 클래스가 싱글톤임이 API에 명백히 드러난다는 점과 간결하다는 장점이 있다.


#### 2. 정적 팩터리 메서드를 public static 멤버로 제공하는 방식
   ```java
   // 코드 3-2 정적 팩터리 방식의 싱글톤
   public class Elvis {
       public static final Elvis INSTANCE = new Elvis();
   
       private Elvis() { }
       public static Elvis getInstance() { return  INSTANCE; }
   
       public void leaveTheBuilding() {
           System.out.println("Whoa baby, I'm outta here!");
       }
   
       // 이 메서드는 보통 클래스 바깥(다른 클래스)에 작성해야 한다!
       public static void main(String[] args) {
           Elvis elvis = Elvis.INSTANCE;
           elvis.leaveTheBuilding();
       }
   }
   ```
   ➡️ Elvis.getInstance는 항상 같은 객체의 참조를 반환하므로 제2의 Elvis 인스턴스란 결코 만들어지지 않는다.  
   ➡️ API를 바꾸지 않고도 싱글톤이 아니게 변경할 수 있다는 장점이 있다.
   ➡️ 싱글톤 클래스를 직렬화하려면 단순히 Serializable을 구현하는 것만으로는 부족하다. 모든 인스턴스 필드를 일시적이라고 선언하고 readResolve 메서드를 제공해야한다. 그렇지 않으면, 직렬화된 인스턴스를 역직렬화할 때마다 새로운 인스턴스가 만들어진다.  
   ➡️ 싱글톤임을 보장해주는 readResolve 메서드는 아래와 같다.
```java
private Object readResolve() {
    // 진짜 Elvis를 반환하고, 가짜 Elvis는 가비지 컬렉터에 맡긴다.
    return INSTANCE;
}
```

#### 3. 원소가 하나인 열거 타입을 선언하는 방법
```java
// 열거 타입 방식의 싱글턴 - 바람직한 방법 (25쪽)
public enum Elvis {
    INSTANCE;

    public void leaveTheBuilding() {
        System.out.println("기다려, 지금 나갈께!");
    }

    // 이 메서드는 보통 클래스 바깥(다른 클래스)에 작성해야 한다!
    public static void main(String[] args) {
        Elvis elvis = Elvis.INSTANCE;
        elvis.leaveTheBuilding();
    }
}
```
➡️ public 필드 방식과 비슷하지만, 더 간결하고 추가 노력 없이 직렬화할 수 있다.
➡️ 아주 복잡한 직렬화 상황이나 리플렉션 공격에서도 제2의 인스턴스가 생기는 일을 완벽히 막아준다.

## ✨ 아이템4. 인스턴스화를 막으려거든 private 생성자를 사용하라.
***
정적 멤버만 담은 유틸리티 클래스는 인스턴스로 만들어 쓰려고 설계한 것이 아니다. 하지만 생성자를 명시하지 않으면 컴파일러가 자동으로 기본 생성자를 만들어준다. 따라서 사용자는 이 생성자가 자동 생성된 것인지 구분할 수 없다. 그래서 이러한 클래스 멤버에 대해 인스턴스화를 막으려면 아래와 같이 private 생성자를 추가해야한다.
```java
// 코드 4-1 인스턴스를 만들 수 없는 유틸리티 클래스 
public class UtilityClass {
    // 기본 생성자가 만들어지는 것을 막는다(인스턴스화 방지용).
    private UtilityClass() {
        throw new AssertionError();
    }

    // 나머지 코드는 생략
}
```
➡️ 이 코드는 어떤 환경에서도 클래스가 인스턴스화 되는 것을 막아주지만 생성자가 분명 존재하는데 호출할 수는 없어 직관적이지 않으니 주석을 달아 놓는것이 좋다.  
➡️ 이 방식은 상속을 불가능하게 하는 효과도 있다.

## ✨ 아이템5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라.
***
클래스가 내부적으로 하나 이상의 자원에 의존하고, 그 자원일 클래스 동작에 영향을 준다면 싱글톤과 정적 유틸리티 클래스는 사용하지 않는 것이 좋다. 해당 자원들을 클래스가 직접 만들게 해서도 안 된다.  
대신 필요한 자원을 생성자에 넘겨주자. 의존 객체 주입이라 하며 클래스의 유연성, 재사용성, 테스트 용이성을 개선해준다.
```java
public class SpellChecker {
  private final Lexicon dictionary;
  
  // 필요한 자원을 생성자에 넘겨주자.
  public SpellChecker(Lexicon dictionary) {
    this.dictionary = Objects.requireNonNull(dictionary);
  }
  
  public boolean isValid(String word) {...}
  public List<String> suggestions(String typo) {...}
}
```

## ✨ 아이템6. 불필요한 객체 생성을 피하라.
***
똑같은 기능의 객체를 매번 생성하기보다는 객체 하나를 재사용하는 편이 낫다. 생성자 대신 정적 팩터리 메서드를 제공하는 불변 클래스에서는 정적 팩터리 메서드를 사용해 불필요한 객체 생성을 피할 수 있다.  
또한 불필요한 객체를 만들어내는 또 다른 예로 오토박싱을 들 수 있다. 오토박싱은 프로그래머가 기본 타입과 박싱된 기본 타입을 섞어 쓸 때 자동으로 상호 변환해주는 기술이다.  
예를들어, sum을 할 때 long이 아닌 Long 으로 선언한다면 불필요한 Long 인스턴스가 약 231개나 만들어져 훨씬 느려진다. 그러므로 박싱된 기본 타입보다는 기본 타입을 사용하고, 의도치 않은 오토박싱이 숨어들지 않도록 주의하자.  

## ✨ 아이템7. 다 쓴 객체 참조를 해제하라.
***
자바는 메모리 관리에 신경을 쓰지 않아도 된다고 생각하는건 큰 오해이다. 메모리 누수는 겉으로 잘 드러나지 않아 시스템에 수년간 잠복하는 사례도 있다. 이런 누수는 철저한 코드 리뷰나 힙 프로파일러 같은 디버깅 도구를 동원해야만 발견되기도 한다. 그래서 이런 종류의 문제는 예방법을 익혀두는 것이 매우 중요하다.  
+ 객체를 다 쓰면 null 처리를 한다. (Best 방법은 아니다)
+ 다 쓴 객체 참조를 해제하는 가장 좋은 방법은 그 참조를 담은 변수를 유효범위 밖으로 밀어내는 것이다.
+ 배열이 비활성 영역이 되는 순간 null 처리해서 해당 객체를 더는 쓰지 않을 것임을 가비지 컬렉터에게 알려야한다.
+ 앤트리가 살아 있는 캐시가 필요한 상황이라면 WeakHashMap을 사용하자. 그러면 다 쓴 엔트리는 그 즉시 자동으로 제거될 것이다.


## ✨ 아이템8. finalizer와 cleaner 사용을 피하라.
***
finalizer 와 cleaner는 자바의 객체 소멸자를 제공한다. 하지만 이 둘다 예측할 수 없으며, 상황에 따라 위험하고, 느리고, 일반적으로 불필요함으로 사용을 피하자.

#### ✔️ finalizer와  cleaner 단점
+ 즉시 수행된다는 보장이 없어 제때 실행되어야 하는 작업은 절대 할 수 없다.
+ 접근할 수 없는 일부 객체에 딸린 종료 작업을 전혀 수행하지 못한 채 프로그램이 중단될 수도 있어서 프로그램 생애주기와 상관없는, 상태를 영구적으로 수정하는 작업에서는 절대 의존해서는 안 된다.

#### 💡 finalizer와  cleaner 의 대안
- AutoCloseable 구현하고 다 사용하면 close 메서드 호출하기
   ```java
   // 코드 8-1 cleaner를 안전망으로 활용하는 AutoCloseable 클래스 (44쪽)
   public class Room implements AutoCloseable {
       private static final Cleaner cleaner = Cleaner.create();
   
       // 청소가 필요한 자원. 절대 Room을 참조해서는 안 된다!
       private static class State implements Runnable {
           int numJunkPiles; // Number of junk piles in this room
   
           State(int numJunkPiles) {
               this.numJunkPiles = numJunkPiles;
           }
   
           // close 메서드나 cleaner가 호출한다.
           @Override public void run() {
               System.out.println("Cleaning room");
               numJunkPiles = 0;
           }
       }
   
       // 방의 상태. cleanable과 공유한다.
       private final State state;
   
       // cleanable 객체. 수거 대상이 되면 방을 청소한다.
       private final Cleaner.Cleanable cleanable;
   
       public Room(int numJunkPiles) {
           state = new State(numJunkPiles);
           cleanable = cleaner.register(this, state);
       }
   
       @Override public void close() {
           cleanable.clean();
       }
   }
   ```
   ➡️ State는 Runnable을 구현하고, 그 안의 run 메서드는 cleanable에 의해 딱 한 번만 호출된다.  
   ➡️ State 인스턴스는 절대로 Room 인스턴스를 참조해서는 안 된다. 순환참조가 생겨 가비지 컬렉터가 Room 인스턴스를 회수해갈 기회가 오지 않기 때문이다.  
   ➡️ 이 Room의 cleaner는 단지 안전망으로만 쓰였다.  cleaner는 이렇게 안전망 역할이나 중요하지 않은 네이티브 자원 회수용으로만 사용하자.


## ✨ 아이템9. try-finally 보다는 try-with-resources를 사용하라
***
자바 라이브러리에는 close 메서드를 호출해 직접 닫아줘야 하는 자원이 많다. InputStream, OutputStream, java.sql.Connection 등이 좋은 예다. 하지만 이것은 성능 문제로 이어지기도 한다.  
전통적으로 자원이 제대로 닫힘을 보장하는 수단인 try-finally와 꼭 회수해야 하는 자원을 다룰 때는 try-finally 말고, try-with-resources를 사용하자. 코드는 더 짧고 분명해지고, 만들어지는 예외 정보도 훨씬 유용하다.  
try-finally 로 작성하면 실용적이지 못할 만큼 코드가 지저분해지는 경우라도, try-with-resources로는 정확하고 쉽게 자원을 회수할 수 있다.

#### ✔️ try-finally
```java
public class TopLine {
    // 코드 9-1 try-finally - 더 이상 자원을 회수하는 최선의 방책이 아니다! (47쪽)
    static String firstLineOfFile(String path) throws IOException {
        BufferedReader br = new BufferedReader(new FileReader(path));
        try {
            return br.readLine();
        } finally {
            br.close();
        }
    }

    public static void main(String[] args) throws IOException {
        String path = args[0];
        System.out.println(firstLineOfFile(path));
    }
}
```

#### ✔️ 자원을 하나 더 사용한 try-finally
```java
public class Copy {
    private static final int BUFFER_SIZE = 8 * 1024;

    // 코드 9-2 자원이 둘 이상이면 try-finally 방식은 너무 지저분하다! (47쪽)
    static void copy(String src, String dst) throws IOException {
        InputStream in = new FileInputStream(src);
        try {
            OutputStream out = new FileOutputStream(dst);
            try {
                byte[] buf = new byte[BUFFER_SIZE];
                int n;
                while ((n = in.read(buf)) >= 0)
                    out.write(buf, 0, n);
            } finally {
                out.close();
            }
        } finally {
            in.close();
        }
    }

    public static void main(String[] args) throws IOException {
        String src = args[0];
        String dst = args[1];
        copy(src, dst);
    }
}
```
➡️ 두번째 예외가 첫 번째 예외를 완전히 집어삼켜, 실제 시스템에서의 디버깅을 몹시 어렵게 할 것이다.
➡️ 이러한 문제를 try-with-resources 로 해결할 수 있다.

#### ✔️ try-with-resources
```java
public class TopLine {
    // try-with-resources - 자원을 회수하는 최선책! 
    static String firstLineOfFile(String path) throws IOException {
        try (BufferedReader br = new BufferedReader(
                new FileReader(path))) {
            return br.readLine();
        }
    }

    public static void main(String[] args) throws IOException {
        String path = args[0];
        System.out.println(firstLineOfFile(path));
    }
}
```

#### ✔️ 복수의 자원을 처리하는 try-with-resources
```java
public class Copy {
    private static final int BUFFER_SIZE = 8 * 1024;

    // 복수의 자원을 처리하는 try-with-resources - 짧고 매혹적이다! 
    static void copy(String src, String dst) throws IOException {
        try (InputStream   in = new FileInputStream(src);
             OutputStream out = new FileOutputStream(dst)) {
            byte[] buf = new byte[BUFFER_SIZE];
            int n;
            while ((n = in.read(buf)) >= 0)
                out.write(buf, 0, n);
        }
    }

    public static void main(String[] args) throws IOException {
        String src = args[0];
        String dst = args[1];
        copy(src, dst);
    }
}
```
➡️ try-with-resources 버전이 짧고 읽기 수월할 뿐 아니라 문제를 진단하기도 훨씬 좋다.

#### ✔️ catch 절과 함께 쓰는 try-with-resources
```java
public class TopLineWithDefault {
    // 코드 9-5 try-with-resources를 catch 절과 함께 쓰는 모습 (49쪽)
    static String firstLineOfFile(String path, String defaultVal) {
        try (BufferedReader br = new BufferedReader(
                new FileReader(path))) {
            return br.readLine();
        } catch (IOException e) {
            return defaultVal;
        }
    }

    public static void main(String[] args) throws IOException {
        String path = args[0];
        System.out.println(firstLineOfFile(path, "Toppy McTopFace"));
    }
}
```































