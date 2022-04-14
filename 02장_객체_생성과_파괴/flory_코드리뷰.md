# 2장. 객체 생성과 파괴

## ✨ 아이템1. 생성자 대신 정적 팩터리 메서드를 고려하라.
***


#### 이전 코드
```java
public class Subject {
  private final String math;  
  private final String english; 

  public Subject(String math, String english) {
    this.math = math;
    this.english = english;
  }

  public static void main(String[] args) {
    Subject subject = new Subject("math", "english");
  }
}
```
<br>

#### 변경 후 코드
```java
public class Subject {
  private final String math;  
  private final String english; 

  public Subject(String math, String english) {
    this.math = math;
    this.english = english;
  }
  
  public static Subject nameOf(String math, String english) {
    return new Subject(math,english);
  }

  public static void main(String[] args) {
    Subject subject = nameOf("math", "english");
  }
}
```
<br>

#### ✔️ 코드리뷰 정리
nameOf 라는 메소드를 통해 과목의 이름이라는 것을 예측할 수 있다. 또한, 호출할 때마다 새로운 객체를 생성할 필요가 없어 불필요한 객체 생성을 줄일 수 있다.


## ✨ 아이템2. 생성자에 매개변수가 많다면 빌더를 고려하라.
***
#### 이전 코드
```java
public class NutritionFacts {
 private final int servingSize;  // (mL, 1회 제공량)     필수
 private final int servings;     // (회, 총 n회 제공량)  필수
 private final int calories;     // (1회 제공량당)       선택
 private final int fat;          // (g/1회 제공량)       선택

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

 public NutritionFacts(int servingSize, int servings, int calories, int fat) {
     this.servingSize  = servingSize;
     this.servings     = servings;
     this.calories     = calories;
     this.fat          = fat;
 }

 public static void main(String[] args) {
     // 순서대로 어떤 값인지 알기 힘듬
     NutritionFacts cocaCola = new NutritionFacts(240, 8, 100, 0);
 }
}
```
<br>

#### 변경 후 코드
```java
public class NutritionFacts {
 private final int servingSize;
 private final int servings;
 private final int calories;
 private final int fat;

 public static class Builder {
     // 필수 매개변수
     private final int servingSize;
     private final int servings;

     // 선택 매개변수 - 기본값으로 초기화한다.
     private int calories      = 0;
     private int fat           = 0;

     public Builder(int servingSize, int servings) {
         this.servingSize = servingSize;
         this.servings    = servings;
     }

     public Builder calories(int val)
     { calories = val;      return this; }
     public Builder fat(int val)
     { fat = val;           return this; }

     public NutritionFacts build() {
         return new NutritionFacts(this);
     }
 }

 private NutritionFacts(Builder builder) {
     servingSize  = builder.servingSize;
     servings     = builder.servings;
     calories     = builder.calories;
     fat          = builder.fat;
 }

 public static void main(String[] args) {
     NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
             .calories(100).build();
 }
}
```
<br>

#### ✔️ 코드리뷰 정리
빌더패턴을 사용하지 않았을 때는 각 매개변수 순서에 맞게 값을 넣어야했는데 해당 순서에 어떤 값이 들어가야 하는지 알기 힘들었다. 하지만 빌더패턴을 사용하니 .calories(100) 이렇게 어떤 데이터인지 알 수 있었고 순서상관없이 지정할 수 있어서 좋았다.


## ✨ 아이템3. private 생성자나 열거 타입으로 싱글톤임을 보증하라.
***
#### 이전 코드
```java
public class Singleton {
   
    public Singleton() {
    }

    public void main(Stirng[] args) {
      Singleton singleton = new Singleton();
    }
}
```
<br>

#### 변경 후 코드
```java
public class Singleton {

    private static Singleton instance = new Singleton();
    
    private Singleton() {
        // 생성자는 외부에서 호출못하게 private 으로 지정해야 한다.
    }

    public static Singleton getInstance() {
        return instance;
    }

    public void main(Stirng[] args) {
      // getInstance 통해서만 객체에 접근가능
      Singleton singleton = Singleton.getInstance();
    }
}
```
<br>

#### ✔️ 코드리뷰 정리
싱글톤 패턴은 다른 클래스에서 정보를 변경이 되면 공유가 어려운 단점을 해결하기위해 사용하지만, 설계상 유일해야하는 시스템 컴포넌트에만 사용하는 것으로 해야한다.

## ✨ 아이템4. 인스턴스화를 막으려거든 private 생성자를 사용하라.
***
#### 이전 코드
```java
public class Computer{
  public Computer() {}

  public static void main(String[] args) {
    Computer computer = new Computer();
  }
}
```
<br>

#### 변경 후 코드
```java
public class Computer{
  private Computer() {}

  public static void main(String[] args) {
    // 생성자를 private으로 변경 함으로써 인스턴스화 할 수 없다.
//    Computer computer = new Computer();
  }
}
```
<br>

#### ✔️ 코드리뷰 정리
객체 생성자를 명시하지 않으면 자동으로 public의 매개변수를 받지 않는 기본 생성자가 만들어진다. 어디서 자동으로 생성되었는지 모르는 것을 방지하기위해 생성자의 타입을 private으로 변경하여 인스턴스화 되는 것을 막아주는 필요가 있을 것 같다.




## ✨ 아이템5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라.
***

#### 의존객체 주입 코드
```java
public class SpellChecker {
  private final Lexicon dictionary;
  
  // 필요한 자원을 생성자에 넘겨주자.
  public SpellChecker(Lexicon dictionary) {
    this.dictionary = Objects.requireNonNull(dictionary);
  }

  public boolean isValid(String word) {
    return true;
  }

  public List<String> suggestions(String typo) {
    return new ArrayList<>();
  }

  public static void main(String[] args) {
    SpellChecker spellChecker = new SpellChecker(new EnglishDictionary());
    boolean isChecked = spellChecker.isValid("hello");
    System.out.println("isChecked = " + isChecked);
  }
}
```
<br>

#### ✔️ 코드리뷰 정리
SpellChecker 클래스를 수정하지 않고 여러 Dictionary 객체를 변경할 수 있기에 유연하다.


## ✨ 아이템6. 불필요한 객체 생성을 피하라.
***
#### 이전 코드
```java
String s = new String("Effective");
```
<br>

#### 변경 후 코드
```java
String s = "Effective";
```
<br>

#### ✔️ 코드리뷰 정리
이전 코드는 실행될 때마다 String 인스턴스를 새로 만든다. 반복문이나 빈번히 호출되는 메서드 안에 있다면 쓸데없는 인스턴스가 수많이 만들어질 수도 있다.  
이런 코드에 대해서는 변경 후 코드같이 하나의 String 인스턴스를 사용하자. 



## ✨ 아이템7. 다 쓴 객체 참조를 해제하라.
***
#### 이전 코드
```java
```
<br>

#### 변경 후 코드
```java
```
<br>

#### ✔️ 코드리뷰 정리



## ✨ 아이템8. finalizer와 cleaner 사용을 피하라.
***
#### 이전 코드
```java
```
<br>

#### 변경 후 코드
```java
```
<br>

#### ✔️ 코드리뷰 정리



## ✨ 아이템9. try-finally 보다는 try-with-resources를 사용하라
***
#### 이전 코드
```java
```
<br>

#### 변경 후 코드
```java
```
<br>

#### ✔️ 코드리뷰 정리