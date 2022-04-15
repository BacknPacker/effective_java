
# 2장 객체 생성과 파괴

## 아이템 1. 생성자 대신 정적 메서드를 고려하라
```java
public static Boolean valueOf(boolean bololean){
  return b Boolean.TRUE : Boolean.FALSE;
}
```
### ✅ 장점 1. 이름을 가질 수 있다.
- 정적 팩터리 메서드는 이름을 통해 반환될 **객체의 특성을 쉽게 묘사**할 수 있다.
- **여러 개의 생성자**가 필요할 것 같으면 생성자를 정적팩터리 메서드로 바꾸고 **반한될 객체의 특성을 나타내는 이름**을 짓자.
- 본인이 아닌 다른 사람이 봤을 때도 의미를 유추할 수 있다. 
```java
public class Card {

    private String color; // 카드 색상
    private int paymentLimit; // 결제한도

    // 시그니처가 동일한 경우 정적 팩터리 메소드를 고려할 것
    public static Card createBlueCard(int paymentLimit) { // 객체의 특성을 고려한 이름 짓는다.
        Card card = new Card();
        card.color = "blue";
        card.paymentLimit = paymentLimit;
        return card;
    }
    
    public static Card createGreenCard(int paymentLimit) {
        Card card = new Card();
        card.color = "green";
        card.paymentLimit = paymentLimit;
        return card;
    }
    Card blueCard = Card.createBlueCard(5000); // 편의상 현재 클래스에 호출
}
```
<hr/>

### ✅ 장점 2. 호출될 때마다 인스턴스를 새로 생성하지는 않아도 된다.
- 불변 클래스는 인스턴스를 미리 만들어놓거나, 인스턴스를 캐싱하여 재활용하는 식으로 **불필요한 객체 생성을 막는다**.
- 언제 어느 인스턴스를 살아 있게 할지를 **철저히 통제 할 수 있다**. -> 인스턴스(instance-controlled) 통제 클래스
- 비슷한 디자인 패턴은 플라이웨이트 패턴(Flyweight pattern)이다
```java
public class Settings {
    public static void main(String[] args) {
        System.out.println(new Settings()); // 아래 결과를 보면 매번 새로운 인스턴스를 생성한다.
        System.out.println(new Settings());
        System.out.println(new Settings());
    }
}
```
![image](https://user-images.githubusercontent.com/53300830/162740301-f4b8f3c4-b072-4517-87f9-a47d83d719ea.png)

```java
public class Settings {
    // private로 객체 생성을 막아 둔 상태
    private Settings() {}
    
    // 미리 만들어둔 인스턴스
    private static final Settings SETTINGS = new Settings();

    // 정적 팩터리를 이용해서 팩터리 내부에서 컨트롤 하겠다는 의미
    public static Settings getInstance() {
        return SETTINGS;
    }
}

public class InstancesTest {
    public static void main(String[] args) {
        // 생성자가 막혀 있기 때문에 Settings 팩터리 내부에 있는 것만 가져올 수 있다.
        // 즉 매번 새로운 Instance를 생성하는게 아닌 가져오는 것만 가능하다.
        Settings instance1 = Settings.getInstance();
        Settings instance2 = Settings.getInstance();

        // 이미 생성된 인스턴스를 갖고오기 때문에 둘의 해쉬코드 값이 동일하다.
        System.out.println("instance1 = " + instance1);
        System.out.println("instance2 = " + instance2);
    }
}
```
![image](https://user-images.githubusercontent.com/53300830/162743280-e5ce3dc5-619d-463f-a090-4fcfb58deb1e.png)
<hr/>

### ✅ 장점 3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.
- API 외견이 작아지고, 접근성이 좋아졌다.
```java
public class Card {
    // 반환할 객체의 타입을 자유롭게 선택할 수 있는 유연성이 있다.
    public List<String> cardList() {
        return new ArrayList<String>();
    }
}
```
<hr/>

### ✅ 장점 4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.
- 반환 타입의 하위 타입이기만 하면 어떤 클래스의 객체를 반환하든 상관없다.
- `EnumSet` 을 예로 새로운 인스턴스를 만들 때 원소의 수에 따라 `RegularEnumSet`(65개이상) / `JumboEnumSet`(64개이하)로 반환된다.
<hr/>

### ✅ 장점 5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.
- 이런 유연함은 서비스 제공자 프레임워크를 만드는 근간이 된다.
- 저자가 제공하는 예시로는 **JDBC**가 있다.
- 서비스 제공자 프레임워크의 컴포넌트
  + 서비스 인터페이스 : 구현체의 동작을 정의 `Connection`
  + 제공자 등록 API : 제공자가 구현체를 등록할 때 사용 `DriverManager.registerDriver`
  + 서비스 접근 API : 클라이언트가 서비스의 인스턴스를 얻을 때 사용 `DriverManager.getConnection`
  + 서비스 제공자 인터페이스 : 서비스 인터페이스의 인스턴스를 생성하는 팩터리 객체를 설명 `Driver`
<hr/>

### ⛔️ 단점 1. 상속을 할려면 public이나 protected 생성자가 필요하기 때문에 정적 팩터리 메소드만 제공하면 하위 클래스를 만들 수 없다.
- 컬렉션 프레임워크의 유틸리티 구현 클래스들은 상속할 수 없다는 의미이다.
- 상속보다 컴포지션을 사용(아이템 18) 하도록 유도하고 불변 타입(아이템 17)으로 만들려면 이 제약을 지켜야 한다는 점에서 오히려 장점으로 받아들일 수도 있다.
<hr/>

### ⛔️ 단점 2. 정적 팩터리 메서드는 프로그래머가 찾기 어렵다.
- API 설명에 드러나지 않으니 사용자는 정적 팩터리 메서드 방식 클래스를 인스턴스화 할 방법을 찾아야한다.

## 아이템 2. 생성자에 매개변수가 많다면 빌더를 고려하라.

### ✅ 1. 정적 팩터리 메소드와 생성자 모두 선택적 매개변수가 많을 때 적절히 대응하기 어렵다는 제약이 존재한다.
- 식품 영양정보를 예시로 들면 아래와 같다.
  + 필수 항목들을 제외하고 선택 항목들은 대부분이 0이다.
  + 하지만 설정하길 원치 않는 매개변수까지 값을 지정해줘야 한다.
  + 매개 변수의 수가 더 많아지면 이를 읽거나 관리하기는 더욱 어려워진다.
  ```java
  public class NutritionFacts {
      // 필수 매개변수
      private final int servingSize; // 1회 제공량
      private final int servings; // 총 n회 체공량

      // 선택 매개변수
      private final int calories; // 1회 제공량당
      private final int fat; // 지방
      private final int sodium; // 나트륨 제공량
      private final int carbohydrate; // 탄수화물 제공량

      // 인스턴스를 만들려면 원하는 매개변수를 모두 포함한 생성자 중 가장 짧은 것을 호출하면 됨
      public NutritionFacts(int servingSize, int servings) {
          this(servingSize, servings,0);
      }

      public NutritionFacts(int servingSize, int servings, int calories) {
          this(servingSize, servings, calories, 0);
      }

      public NutritionFacts(int servingSize, int servings, int calories, int fat) {
          this(servingSize, servings, calories, fat, 0);
      }

      public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium) {
          this(servingSize, servings, calories, fat, sodium, 0);
      }

      public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate) {
          this.servingSize = servingSize;
          this.servings = servings;
          this.calories = calories;
          this.fat = fat;
          this.sodium = sodium;
          this.carbohydrate = carbohydrate;
      }
  }
  ```
  ```java
  public static void main(String[] args) {
      // 이 코드는 1회 제공량이 150일까 아니면 제공 횟수가 150인걸까?
      NutritionFacts nutritionFacts1 = new NutritionFacts(150, 2, 0);
      // 이 코드는 나트륨 제공량이 몇 일까? 그리고 0인 값을 꼭 적어줘야 하는 것일까
      NutritionFacts nutritionFacts2 = new NutritionFacts(150, 2, 100, 0, 12, 10);
  }
  ```
  > 점층적 생성자 패턴도 쓸 수는 있지만, 매개변수 개수가 많아지면 클라이언트 코드를 작성하거나 읽기 어렵다.

<hr/>

### ✅ 2. 자바빈즈(JavaBeans Pattern)
**자바빈즈 패턴은 매개변수가 없는 생성자를 만든 후, 세터(setter) 메소드를 호출하여 원하는 매개변수의 값을 설정하는 방식이다.**
- 점층적 생성자 패턴의 단점이 보완된다.
- 객체 하나를 만들려면 메서드를 여러 개 호출해야 하고, 객체가 완성되기 전에는 일관성이 무너진 상태에 놓이게 된다.
> 이러한 단점을 보완해주고자 얼리고(freezing) 얼리기전에는 사용 할 수 없도록 할 수 있다.  
> 하지만 런타임 오류에 취약하다는 단점이 있다.  
> 어떤 방식인지는 찾아봐도 나오는 내용이 없어서 추후에 찾아볼 예정
```java
public class NutritionFacts {
    // 필수 매개변수
    private int servingSize = -1; // 1회 제공량
    private int servings = - 1; // 총 n회 체공량

    // 선택 매개변수
    private int calories = 0; // 1회 제공량당
    private int fat = 0; // 지방
    private int sodium = 0; // 나트륨 제공량
    private int carbohydrate = 0; // 탄수화물 제공량

    public NutritionFacts() {}

    // setter 생성
    public void setServingSize(int val) {servingSize = val;}
    public void setServings(int val) {servings = val;}
    public void setCalories(int val) {calories = val;}
    public void setFat(int val) {fat = val;}
    public void setSodium(int val) {sodium = val;}
    public void setCarbohydrate(int val) {carbohydrate = val;}
}
```
```java
public static void main(String[] args) {
    // 점층적 생성자 패턴과는 다르게 원하는 필드만 골라서 초기화 할 수 있다.
    NutritionFacts burger = new NutritionFacts();
    burger.setServingSize(150);
    burger.setServings(1);
    burger.setCalories(250);
    burger.setSodium(35);
    burger.setCarbohydrate(100);
}
```
<hr/>

### ✅ 3. 빌더 패턴
점층적 생성자 패턴의 안정성 + 자바빈즈 패턴의 가독성을 합쳐놓은 패턴이다.
- 클라이언트가 필요한 객체를 만드는 것이 아니라 빌더 객체를 생성 후 매개변수를 설정하고 객체를 반환하는 방식
- 일종의 setter 메서드로 매개변수 값을 설정한 뒤, build 메서드를 호출하여 객체를 반환한다. 
```java
public class NutritionFacts {
    private final int servingSize; // 1회 제공량
    private final int servings; // 총 n회 체공량
    private final int calories; // 1회 제공량당
    private final int fat; // 지방
    private final int sodium; // 나트륨 제공량
    private final int carbohydrate; // 탄수화물 제공량

    // 검증식
    private NutritionFacts(Builder builder) {
        servingSize = builder.servingSize;
        servings = builder.servings;
        calories = builder.calories;
        fat = builder.fat;
        sodium = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }

    public static class Builder {
        // 필수 매개변수
        private final int servingSize;
        private final int servings;
        // 선택 매개변수
        private int calories = 0;
        private int fat = 0;
        private int sodium = 0;
        private int carbohydrate = 0;

        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings    = servings;
        }

        // setter의 장점
        public Builder calories(int val) {
            calories = val;
            return this;
        }
        public Builder fat(int val) {
            fat = val;
            return this;
        }
        public Builder sodium(int val) {
            sodium = val;
            return this;
        }
        public Builder carbohydrate(int val) {
            carbohydrate = val;
            return this;
        }

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }
    public static void main(String[] args) {
        NutritionFacts burger = new NutritionFacts.Builder(500, 1)
                .calories(200)
                .sodium(35)
                .carbohydrate(15)
                .build();
    }
    // TODO 계층적으로 설계된 클래스와 함께 쓰기 좋다.
}
```

## 아이템 3. private 생성자나 열거 타입으로 싱글턴임을 보증하라

### ❓ 싱글턴이란?
인스턴스를 **오직 하나만 생성할 수 있는 클래스**를 말한다.
ex) 무상태 객체, 설계상 유일해야 하는 시스템, 컴포넌트
### ⛔️ 싱글턴의 단점
**싱글턴을 사용하는 클라이언트 테스트가 어렵다**. 하나의 자원을 공유하기 때문에 테스트로 써도 되는 클래스인가를 판별해야 한다.
ex) **실제 DB에 접근**하는 싱글턴을 테스트에 적용하면 안된다.

### ✅ 싱글턴을 만드는 방법 1. 인스턴스를 public static final 필드로 만들고 생성자를 private으로 한다. 
```java
public class SingletonTest {

    public static final SingletonTest INSTANCE = new SingletonTest();

    private SingletonTest() {}
}
```
```java
@Test
public void 싱글턴테스트() {
    SingletonTest instance1 = SingletonTest.INSTANCE;
    SingletonTest instance2 = SingletonTest.INSTANCE;

    assertEquals(instance1, instance2);
}
```
![image](https://user-images.githubusercontent.com/53300830/162971197-8262013e-38a6-435d-a229-af3d9e07df39.png)
> 간결하고 누가봐도 싱글턴 클래스임을 알 수 있다. 
#### 예외가 존재한다. 
- 권한이 있는 클라이언트는 리플렉션 API(아이템65) AccessibleObject.setAccessiable를 사용해서 private 생성자 호출
<hr/>

### ✅ 싱글턴을 만드는 방법 2. 정적 팩터리 메소드를 public static 멤버로 제공
```java
public class SingletonTest2 {
    private static final SingletonTest2 INSTANCE = new SingletonTest2();

    private SingletonTest2() {}

    public static SingletonTest2 getInstance() {
        return INSTANCE;
    }
}
```
```java
@Test
public void 싱글턴테스트2() {
    SingletonTest2 instance1 = SingletonTest2.getInstance();
    SingletonTest2 instance2 = SingletonTest2.getInstance();

    assertEquals(instance1, instance2);
}
```
![image](https://user-images.githubusercontent.com/53300830/162974187-3b8fd982-815e-4c56-b022-94285f4dcb57.png)
> 팩터리메서드만 수정하면 언제든지 싱글톤이 아니게 바꿀 수 있고, 타입에 유연하게 대처 할 수 있으며, 공급자로 만들 수 있다.
#### 두 방식의 문제점
  1. 역직렬화할 때 새로운 인스턴스를 만들어 반환한다.
  2. 리플렉션에 대한 추가 처리가 필요하다.
  3. 직렬화, 역직렬화에 대한 추가 처리가 필요하다.

<hr/>

### ✅ 싱글턴을 만드는 방법 3. 원소가 하나인 enum 타입 선언
```java
public enum Singleton{
  INSTANCE // 기본적으로 직렬화가 돼 있다.
}
```

## 아이템 4. 인스턴스화를 막으려거든 private 생성자를 사용하라.
> 앞서 인스턴스를 막아야 하는 상황이 무엇일까 생각이 들어 찾아보았습니다.
> 아이템 3(싱글톤 패턴)을 쓰는 이유와 비슷했습니다.
> 1. 메모리 낭비 방지
> 2. 객체 로딩시간이 절대적으로 중요한 경우  
> [추가적으로 궁금했던 내용들](https://github.com/BacknPacker/effective_java/issues/8)

개발을 진행하다보면 인스턴스 변수와 메소드가 필요없는 유틸리티 클래스가 필요한 순간이 있다. <br/>
예시 : `java.lang.Math`, `java.util.Arrays`

### ✅ 추상 클래스로 만드는 것으로는 인스턴스화를 막을 수 없다.
개념적으로 추상클래스는 인스턴스로 만들 수 없다. 하지만 추상클래스는 상속한다면 상황이 달라진다.
>인스턴스화를 막기 위해 추상클래스를 만들게 되면, 사용자는 상속해서 쓰라는 뜻으로 오해할 수 있다.
```java
public class NoInstance {
    // 인스턴스화를 막기
    private NoInstance() {
        // 꼭 필요는 없지만 실수로라도 생성자를 호출하지 않도록 도와준다.
        // 명시적으로 적어주기
        throw new AssertionError();
    }
}
```
## 아이템 5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라.
> 사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글턴 방식이 적합하지 않다.

#### 정적 유틸리티 잘못 사용한 예 - 유연하지 않고 테스트 하기 어렵다.
```java
// 정적 유틸리티를 잘못 사용한 예 - 유연하지 않고 테스트하기 어렵다
public class SpellChecker{
  private static final Lexicon dictionary = ...; // 사전에 의존한다.
  
  private SpellChecker() {} // 객체 생성 방지
  
  public static boolean isValid(String word){...}
  public static List<String> suggestions(String typo){...}
}
```
#### 싱글턴을 잘못 사용한 예 - 유연하지 않고 테스트 하기 어렵다.
```java
// 싱글턴을 잘못 사용한 예 - 유연하지 않고 테스트하기 어렵다
public class SpellChecker{
  private static final Lexicon dictionary = ...; // 사전에 의존한다.
  
  private SpellChecker(...) {} 
  public static SpellChecker INSTANCE = new SpellChecker(...);

  public static boolean isValid(String word){...}
  public static List<String> suggestions(String typo){...}
}
```
> 테스트 코드 작성의 문제와 사전을 언어별로 보고 싶은 경우 문제가 생긴다.
> 필드에서 final 한정자를 제거하고 다른 메서드를 추가하는 방식도 가능하지만 어색하고 오류를 내기 쉬우며, 멀티스레드 환경에서는 쓸 수 없다.
> 
<hr/>

### ✅ SpellChecker가 여러 사전을 이용하도록 수정
- SpellCheck가 여러 자원 인스턴스를 지원해야 한다.
- 클라이언트가 원하는 자원(dictionary)을 사용해야 한다.
> 인스턴스를 생성할 때 생성자에 필요한 자원을 넘겨주면 해결이 된다.
#### 해결 방법 : 의존 객체 주입 방식을 이용한다.
```java
// 의존 객체 주입은 유연성과 테스트 용이성을 높여준다 
public class SpellChecker{
  private final Lexicon dictionary;
  
  public SpellChecker(Lexicon dictionary){ // 의존 객체 주입
    this.dictionay = Objects.requiredNonNull(dictionay);
  }
  
  public boolean isValid(String word){...}
  public List<String> suggestions(String typo){...}
}
```
- 자원이 몇개든, 의존관계가 어떻든 상관이 없다.
- 불변을 보장하여 같은 자원을 사용하는 여러 사용자가 안심하고 공유할 수 있다.
<hr/>

### ✅ 매개변수로 자원 팩터리를 넘겨주는 방식
- 대표적으로 Java8에 소개된 Supplier<T> 인터페이스가 있다.
```java
// 클라이언트가 제공한 팩터리가 생성한 Tile들로 구성된 Mosaic를 만드는 메서드이다.
Mosaic create(Supplier<? extends Tile> tileFactory) { ... }
```
> 의존 객체 주입은 유연성과 테스트 용이성을 개선해준다.  
하지만 의존성이 수백 수천개가 되는 프로젝트에서는 코드를 어지럽게 만들기도 한다.  
스프링(Spring)같은 의존 객체 프레임워크를 사용하면 위 단점을 해소할 수 있다.

## 아이템 6. 불필요한 객체 생성을 피하라.
> 똑같은 기능을 하는 객체의 경우 재사용 하는 편이 빠르고 세련돼 보인다.  
특히 불변 객체(아이템17)는 어디든 재사용 할 수 있다.

### ✅ 불필요한 인스턴스 생성
```java
@Test
public void 객체_재사용_테스트() {
    String name1 = new String("ji-hoon");
    String name2 = new String("ji-hoon");

    assertThat(name1).isSameAs(name2); // 새로운 인스턴스를 생성하므로 false
}
```
![image](https://user-images.githubusercontent.com/53300830/163175180-5fe4b4c9-d23a-45f8-b31b-3ad1cb55aee0.png)
> 극단적인 예시이지만 위 테스트를 보면 굳이 새로 생성 할 필요가 없는데 다시 인스턴스가 생성 되는 것을 볼 수 있다.

### 📋 해결코드
```java
@Test
public void 객체_재사용_테스트_성공() {
    String name1 = "ji-hoon";
    String name2 = "ji-hoon";

    assertThat(name1).isSameAs(name2); // 인스턴트 재사용하므로 true
}
```
![image](https://user-images.githubusercontent.com/53300830/163176166-b2e3a054-c2e3-4239-ba0a-a4a12a5b5cbc.png)
> 새로운 인스턴스를 매번 만드는 대신 하나의 String 인스턴스를 사용함으로 같은 객체를 재사용함이 보장된다.
<hr/>

### ✅ 생성 비용이 비싼경우 객체를 재사용하자.
비싼 객체는 반복해서 필요하다면 캐싱해서 재사용해야 한다.
```java
// 6-1. 성능을 훨씬 더 올릴 수 있다.
// String.matchers 정규 표현식으로 문자열 형태를 확인하는 가장 쉬운 방법이지만, 성능이 중요한 상황에서 반복 사용하기에 좋지는 않다.
// 한 번 쓰고 버려져서 가바지 컬렉션 대상이 되기 때문
static boolean isRomanNumeralSlow(String s) {
    return s.matches("^(?=.)M*(C[MD]|D?C{0,3})(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
}

// 6-2. 값비싼 객체를 재사용해 성능을 개선한다.
// 불변인 Pattern 인스턴스를 클래스 초기화(정적) 과정에서 직접 생성해 캐싱해두고, 나중에 호출할때 재사용한다.
private static final Pattern ROMAN = Pattern.compile(
        "^(?=.)M*(C[MD]|D?C{0,3})(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");

static boolean isRomanNumeralFast(String s) {
    return ROMAN.matcher(s).matches();
}

@Test
@DisplayName("6-1 객체 재사용 테스트(slow)")
public void 객체_재사용_1() {
    for (int i = 0; i < 10000; i++) { // 개선 전 시간 체크 
        isRomanNumeralSlow("By promptly ~~ .");
    }
}

@Test
@DisplayName("6-2 객체 재사용 테스트(fast)")
public void 객체_재사용_2() {
    for (int i = 0; i < 10000; i++) { // 개선 후 시간 체크
        isRomanNumeralFast("By promptly ~~ .");
    }
}
```
![image](https://user-images.githubusercontent.com/53300830/163182282-33d75745-8c57-4c39-9e2d-10e206074220.png)
![image](https://user-images.githubusercontent.com/53300830/163182323-87f4d341-495e-4036-ab33-6096240414d4.png)
> 개선 후 코드(isRomanNumeralFast)가 빠른 것을 볼 수 있다.  

<hr/>

### ✅ 같은 인스턴스를 대변하는 여러개의 인스턴스를 생성하지 않도록 하자.
예시로 Map 인터페이스의 keySet 메서드는 Map 객체 안의 키 전부를 담은 Set 뷰를 반환한다.

```java
@Test
@DisplayName("keySet 메서드는 같은 Map인스턴스를 대변한다. ")
public void keySet() {
    Map<String, Object> instanceTest = new HashMap<>();
    instanceTest.put("lee-ji-hoon", "student");

    // 이전 String 예제를 보면 새로운 Set 인스턴스가 생성 될 것이라고 예상된다.
    Set<String> instanceSetTest1 = instanceTest.keySet();
    Set<String> instanceSetTest2 = instanceTest.keySet();

    // 하지만 Map에서 호출하는 keySet메서드는 같은 Map을 대변하기 때문에 새로운 인스턴스가 생성되지 않는다.
    Assertions.assertThat(instanceSetTest1).isSameAs(instanceSetTest2); // true
}
```
![image](https://user-images.githubusercontent.com/53300830/163186153-662763bb-6fc6-4d7d-8e8a-5f6f4530caa5.png)
```java
// 추가 
instanceSetTest1.remove("ka");
// 재사용하는 Map일 경우 다른쪽에도 영향을 준다.
// instanceSetTest1.remove 지만 instanceSetTest2 에도 영향을 주는 모습을 볼 수 있다.
Assertions.assertThat(instanceSetTest2).size().isEqualTo(2); // false -> size = 1
```
![image](https://user-images.githubusercontent.com/53300830/163186304-ec3c9753-566f-4aaf-80d1-423a187f1349.png)
> 위 처럼 같은 Map을 대변하기 때문에 재사용할 때 위험한 부분이 있기도 하다.
<hr/>

### ⛔️ 오토 박싱,언박싱을 주의하자.
불필요한 박싱/언박싱은 불필요한 메모리 할당, 재할당을 반복하게 된다.
박싱 타입이 필요한 경우가 아니라면 기본타입을 사용해야 한다.
<details>
<summary>오토박싱,언박싱</summary>
<div markdown="1">

> 박싱 : 기본 타입 데이터에 대응하는 Wrapper 클래스로 만드는 동작  
언박싱 : Wrapper 클래스에서 기본 타입으로 변환
- 기본 타입 : int, long, float, double, boolean 등
- Wrapper 클래스 : Integer, Long, Float, Double, Boolean 등

```java
  // 오토 박싱
  int i = 10;
  Integer num = i;

  // 오토 언박싱
  Integer num = new Integer(10);
  int i = num;
```

</div>
</details>

```java
class AutoBoxingExampleTest {
    @Test
    @DisplayName("오토박싱 테스트")
    public void 오토박싱() {
        Long sum = 0L;
        for (long i = 0; i <= Integer.MAX_VALUE; i++) sum += i; // 오토박싱 발생
    }
    @Test
    @DisplayName("오토박싱 해결 테스트")
    public void 오토박싱해결() {
        long sum = 0L; // Long -> long 변경
        for (long i = 0L; i <= Integer.MAX_VALUE; i++) sum += i; // long 타입 통일로 인해 오토박싱 해결
    }
}
```
![image](https://user-images.githubusercontent.com/53300830/163193780-a323cab6-6d15-4598-baa6-2f697abf81b4.png)
![image](https://user-images.githubusercontent.com/53300830/163193733-5e0a4840-7755-41ac-9f81-dec9b760adcd.png)
> 타입을 프리티머브 타입으로 바꾸면서 성능이 확실히 개선 된 모습을 볼 수 있다.

## 아이템 7. 다 쓴 객체 참조를 해제하라.
> JVM은 GC가 메모리를 관리해준다.  
하지만 메모리 관레에 더 이상 신경 쓰지 않아도 된다는 것은 아니다  
[참고 블로그](https://mangkyu.tistory.com/118)

아래 사진 처럼 간단한 스택 코드로 예시를 들어보겠습니다.

### ✅ Stack 테스트
```java
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    // 원소 추가
    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }
    // TODO 개선 필요
    public Object pop() {
        if (size == 0) {
            throw new EmptyStackException();
        }
        return elements[--size];
    }

    /**
     * 원소를 위한 공간 확보
     */
    private void ensureCapacity() {
        if (elements.length == size) {
            elements = Arrays.copyOf(elements, 2 * size + 1);
        }
    }
}
```
```java
@Test
@DisplayName("stack GC 테스트")
void 스택_테스트() {
    Stack stack = new Stack();
    // 1~5 까지 5개의 값 넣기
    for (int i = 1; i < 6; i++) stack.push(i);
    // 5개의 값 제거
    for (int i = 1; i < 6; i++) {
        stack.pop();
    }
}
```

![image](https://user-images.githubusercontent.com/53300830/163326919-12432e4d-3cb2-4f67-ad1f-00c12b0a91c9.png)

> 테스트 코드 결과입니다. i가 5일때 0~4번 index의 elements값이 남아있습니다.

- 모든 값을 `pop()` 메서드를 통해 없앴다고 생각했지만 실제로는 남아 있는 것을 확인 할 수 있었다.
- 드문 경우긴 하지만 심할 때는 디스크 페이징이나 `OutOfMemoryError를` 일으켜 프로그램이 예기치 않게 종료 될 수 있다.

#### 표로 그려보자면 아래와 같습니다. 편의상 size는 그대로 두겠습니다.

|Index|0|1|2|3|4(pop)</span>
|---|---|---|---|---|---|
|Value|1|2|3|4|5|

|Index|0|1|2|3(pop)|4(garbage)|
|---|---|---|---|---|---|
|Value|1|2|3|4|5(garbage)|

|Index|0|1|2(pop)|3(garbage)|4(garbage)|
|---|---|---|---|---|---|
|Value|1|2|3|4(garbage)|5(garbage)|

<hr/>

### 📋 해결 방안

```java
// 원소 제거 개선 코드
public Object pop() {
    if (size == 0) {
        throw new EmptyStackException();
    }
    Object result = elements[--size];
    elements[size] = null; // 다 쓴 참조 해제
    return result;
}
```

![image](https://user-images.githubusercontent.com/53300830/163326778-2cfb7e90-41b0-4dcd-9c09-5b6ea035c3a7.png)

> 전과 동일한 테스트 코드 결과입니다. i가 5 일때 0~4번 index의 elements값이 null처리 된 것을 볼 수 있습니다.

#### 표로 그려보자면 아래와 같습니다. 편의상 size는 그대로 두겠습니다.

|Index|0|1|2|3|4(pop)</span>
|---|---|---|---|---|---|
|Value|1|2|3|4|5|

|Index|0|1|2|3(pop)|4|
|---|---|---|---|---|---|
|Value|1|2|3|4|null|

|Index|0|1|2(pop)|null|null|
|---|---|---|---|---|---|
|Value|1|2|3|null|null|

<hr/>

### ❓ 모든 것을 null로 처리하는 것이 옳은 코드 인것인가?
> 대답은 바로 No이다.

- 프로그램을 필요 이상으로 지저분하게 만들 뿐이다.
- 객체 참조를 `null`처리하는 일은 예외적인 경우여아한다.
- 다 쓴 참조를 해제하는 가장 좋은 방법은 그 참조를 담은 변수를 유효 범위(scope) 밖으로 밀어내는 것이다.
- 아이템57을 제대로 정의했다면 자연스럽게 이루어진다.
> Stack 클래스는 자기 메모리를 직접 관리하기 때문에 메모리 누수에 취약하다.  
그러므로 개선코드처럼 null을 참조함으로써 GC에게 비활성 영역이라는 것을 알려줘야 한다.

<hr/>

### ✅ 메모리 누수가 발생하는 원인과 해결책
다 쓴 참조(obsolete reference)를 여전히 가지고 있다는 입니다.  
즉 `elements` 배열의 활성 영역 밖의 참조들이 모두 여기에 해당한다는 뜻이기도 합니다.

> 의도치 않게 객체를 살려두는 메모리 누수를 찾는 것은 아주 까다로워서 철저한 코드리뷰나 힘 프로파일러 같은 디버깅 도구를 동원해야만 발견되기도 한다.

#### 원인 1. 자기 메모리를 직접 관리하는 클래스에서 관리를 못해주는 경우
- 위 Stack 처럼 자기 메모리를 직접 관리하는 경우 더 이상 참조되지 않는 객체를 참조해제 해주지 않는다면 
GC는 해당 객체가 객체 배열에서 참조하고 있기 때문에 실제로는 사용하지 않을지라도 수거하지 않는다.
> 유효 범위 밖이거나 쓸 일이 없어지는 객체는 Null처리를 해줘서 해결해야한다.

#### 원인 2. 캐시 역시 메모리 누수를 일으키는 주범이다.
- 객체 참조를 캐시에 넣고, 그 부분을 잊어버린다면 객체를 사용한 뒤에도 참조 해제가 되지않기에 메모리에 쌓이기만한다.

- 해결책 2-1) 엔트리가 살아있는 캐시가 필요한 상황이라면 WeakHashMap을 사용하자.
  - Map 이므로 Key와 Value를 한쌍의 데이터로 관리한다.
  - Key에 대한 참조가 더 이상 존재하지 않게 되면, Value를 가져 올 수 있는 방법이 없다고 판단 -> Key-Value 자동으로 삭제 되는 MAP이다.

[조금 늦은, IT 관습 넣기(JS.Kim) Java – Collection – Map – WeakHashMap](https://blog.breakingthat.com/2018/08/26/java-collection-map-weakhashmap/)
```java
public static void main(String[] args) {
    WeakHashMap<Integer, String> map = new WeakHashMap<>();
    Integer key1 = 1000;
    Integer key2 = 2000;

    map.put(key1, "test a");
    map.put(key2, "test b");

    key1 = null; // (key1,"test a") 자동으로 삭제

    System.gc();  // 강제 Garbage Collection 호출

    map.entrySet().forEach(el -> System.out.println(el));
}
```
![image](https://user-images.githubusercontent.com/53300830/163341624-43fd86ad-bc95-463d-8ba3-df88a6ad0d2d.png)
> key1과 key1.value 값이 사라진 것을 볼 수 있다.

```java
public static void main(String[] args) {
    HashMap<Integer, String> map = new HashMap<>();
    Integer key1 = 1000;
    Integer key2 = 2000;

    map.put(key1, "test a");
    map.put(key2, "test b");

    key1 = null; // null 처리해줬지만 HashMap에서는 동기화가 되지 않는다.

    System.gc();  // 강제 Garbage Collection 호출

    map.entrySet().forEach(el -> System.out.println(el));
}
```

![image](https://user-images.githubusercontent.com/53300830/163341874-73c633e1-46fc-4806-98d9-de0a8527c3d0.png)
> key1.value 및 size 전부 살아있는 모습을 볼 수 있다.

- 해결책 2-2) 백그라운드 스레드(ScheduledThreadPoolExecutor)를 활용해 캐시에 새 엔트리를 추가하는 등 캐시에 접근하는 일이 있을 때 부수 작업 수행
- 해결책 2-3) LinkedHashMap의 removeElderstEntry 메서드를 써서 처리한다.

#### 원인 3. 리스너(listener)와 콜백(callback)
- 클라이언트에서 콜백을 등록만하고 명확히 해지하지 않는다면 점점 쌓이게 된다.

    <details>
    <summary>리스너(listener), 콜백(callback)</summary>
    <div markdown="1">

        [참고 블로그](https://www.crocus.co.kr/1533)

        - 콜백(Callback) : 이벤트가 발생하면 특정 메소드를 호출해 알려준다. (1개)  
        - 리스너(Listener) : 이벤트가 발생하면 연결된 리스너(핸들러)들에게 이벤트를 전달한다 (n개)

    </div>
    </details>

- 해결책 3) 콜백을 약한 참조(weak refernce)로 저장하면 가비지 컬렉터가 즉시 수거해간다.
> 예를 들어 WeakHashMap에 키로 저장하면 된다.

## 아이템 8. finalizer와 cleaner 사용을 피하라
> 자바에서 객체 소멸은 GC가 담당하고, 비메모리 자원회수는 try-with-recources, try-finally로 해결한다.  

### ✅  finalizer
  - 예측할 수 없고 상황에 따라 위험할 수 있어서 일반적으로 불필요.
  - 오작동, 낮은 성능, 이식성 문제의 원인이 됨
  - 자바9 부터 `deprecated API`로 지정하며 `cleaner를` 대안으로 사용
### ✅  cleaner 
  - `finaliezer` 보다 덜 위험하지만 여전히 예측할 수 없고, 느리고, 똑같이 일반적으로 불필요함.

<hr/>

### ⛔️ finalizer && cleaner 의 문제
#### 문제 1. 자바에서 접근할 수 없게 된 객체는 가비지 컬렉터가 회수하고 프로그래머는 아무런 작업도 하지 않아도 된다.
  > 만약 비메모리 자원을 회수해야 한다면 try-with-resources 와 try-finally를 이용해 해결해야 한다.
#### 문제 2. 예측할 수 없다 
  - 내가 원하는 때 즉시 실행 되어야 하는 작업은 절대 할 수 없다.
  - `finalizer`는 다른 쓰레드보다 우선순위가 낮아 실행될 기회를 얻지 못하고 쌓이다가 `OutOfMemoryError`가 발생할 수 있다. 
#### 문제 3. 상태를 영구적으로 수정하는 작업에서는 절대 `finalizer`나 `cleaner`에 의존해서는 안된다.
  - 예시로 데이터베이스의 공유 자원의 락(lock) 해제를 `finalizer`나 `cleaner`에게 맡기면 분산 시스템 전체가 서서히 멈출 수 있다.
    > 자바 언어 명세는 `finalizer`와 `cleaner`의 수행 시점,여부 조차 보장하지 않는다.
  - System.gc나 system.runFinalization 메서드를 사용하더라도 finalizer나 cleaner의 실행 가능성을 높여줄 수는 있으나 실행을 보장해주진 않는다.
#### 문제 4. finalizer와 cleaner은 심각한 성능 문제도 동반한다.
  - 책의 예시로는 `AutoCloseable` 객체를 생성하고 GC가 수거하기 까지 : 12ns걸리지만, `finalizer`를 사용하면 550ns가 걸린다. 
  - 안전망을 설치하는 대가로 성능이 약 5배 정도 느려진다는 이야기이다.
#### 문제 5. finalizer를 사용한 클래스는 finalizer 공격에 노출되어 심각한 보안 문제를 일으킬 수 있다.
  - 생성자나 직렬화 과정에서 예외가 발생하면 `finalizer`가 수행되는데   
`finalizer`를 악의적으로 오버라이딩한 하위 클래스의 `finalizer`가 수행 될 수 있다.
    > finalizer를 final로 선언하여 오버라이딩하지 못하게 하는 것으로 해결 할 수 있다.
    > [finalizer로 어떻게 공격을 하는 것인가 [나만의 인덱스 블로그]](https://yangbongsoo.tistory.com/8?category=919799) 


<hr/>

### ❓ 대안이 있는것인가
#### 대안 1. AutoCloseable
  - 파일이나 쓰레드 등 종료해야 할 자원에 `AutoCloseable`을 구현한다.
  - 그 뒤 `close` 메소드를 호출하면 된다.
#### 대안 2. try-with-resources를 사용해 자동으로 close될 수 있도록 한다.
> 각 인스턴스는 자신이 닫혔는지 추적하는 것이 제일 좋다.

<hr/> 

### ❓ 언제 사용하는 것일까?
#### 사용처 1. AutoCloseable를 구현하지 않았을 경우를 대비한 안전망 역할이 필요할 때
  - 자바 라이브러리의 `FileInputStream`, `FileOutputStream`, `ThreadPoolExecutor` 대표적
#### 사용처 2. 네이티브 피어와 연결된 객체

  <details>
    <summary>네이티브 피어란?</summary>
    <div markdown="1">
      - 네이티브 피어는 `C/C++` 혹은 `어셈블리 프로그램`을 컴파일한 기계어 프로그램을 지징합니다. <br/>
      - 이를 라이브러리로써 `자바 피어`가 실행 할 수 있게 해주는 인터페이스를 `JNI(Java Native Interface)`라고 합니다. <br/>
      - 자바 피어가 정적으로 `System.loadLibrary()` 메소드를 호출해 `네이티브 피어`를 로딩하고 `네이티브 메소드`는 `native 키워드`를 사용해 호출 하는 방식으로 사용한다.

      > 대부분 maven central에서 가져올 자바 라이브러리를 사용하기 때문에 실상 JNI를 쓸 일은 거의 없다고 합니다.
    </div>
  </details>

  - 자바 객체가 아니기 때문에 GC에서는 이 존재를 알지 못함 -> `cleaner` 혹은 `finalizer`가 나서서 처리하기에 적절하다.
  > 단 성능저하를 감당할 수 없거나 네이티브 피어가 사용하는 자원을 `즉시 회수`해야 한다면 앞서 설명한 `close` 메서드를 사용해야만 한다.

### ✅  예제

```java
public class Room implements AutoCloseable{
    private static final Cleaner cleaner = Cleaner.create();

    // Room을 참조해서는 안된다.
    // Room을 참조하게 되면 순환 참조가 일어나게 된다.
    // -> 
    private static class State implements Runnable {
        int numJunkPiles; // 수거할 자원

        public State(final int numJunkPiles) {
            this.numJunkPiles = numJunkPiles; // 수거대라고 생각
        }
        /**
         * 1. close() 를 호출 할 때
         * 2. cleaner(안전망)
         */
        @Override
        public void run() {
            System.out.println("방 청소 진행 중");
            numJunkPiles = 0;
        }
    }

    private final State state;

    private final Cleaner.Cleanable cleanable;

    public Room(final int numJunkFiles) {
        this.state = new State(numJunkFiles);
        cleanable = cleaner.register(this, state); // Runnable 객체를 등록
    }

    @Override
    public void close() throws Exception {
        cleanable.clean();
    }
}
```
### ✅  test 코드
``` java 
@Test
@DisplayName("방 청소 테스트")
void autoClean() throws Exception {
    // 잘 짜여진 클라이언트 코드 예시 (try-with-resources)
    try (Room myRoom = new Room(7)) {
        System.out.println("청소 시작");
    } finally {
        System.out.println("청소 완료");
    }
}

@Test
@DisplayName("방 청소 테스트 언제 할거야?")
void notClean() throws Exception {
    Room room = new Room(7);
    // "방 청소 진행 중"은 절대로 출력되지 않는다.
    // 직접 close를 호출해야만 한다.
    // room.close(); 
    System.out.println("제발 청소 좀 해");
}
```
![image](https://user-images.githubusercontent.com/53300830/163536949-d3d88d40-4efc-483d-8621-199d630fcfad.png)
![image](https://user-images.githubusercontent.com/53300830/163539608-7a9012b9-1d22-4709-950b-4758c868f572.png)

> 첫 번째는 try-with-resources로 잘 짜여진 예시이며, 두 번째는 절대로 사용하면 안되는 예시이다.  
언제 실행될지 예측을 할 수 없기 때문이다.

## 아이템9. try-finally 보다는 try-with-resources를 사용하라.

### 자바 라이브러리에서 close 메서드를 호출해 직접 닫아줘야 하는 자원들이 있다.
- `InputStream` `OutputStream` `java.sql.Connection`
- 이러한 자원들을 닫아주는 클라이언트가 놓치기 쉬워 예측할 수 없는 성능 문제로 이어지기도 한다.
- 상당수가 안전망으로 finalizer를 활용하지만 `아이템 8` 에서 이야기하듯 사용하는 것에는 단점이 너무 많다.
> close 해주기 위해 try-finally가 사용 됐다.



