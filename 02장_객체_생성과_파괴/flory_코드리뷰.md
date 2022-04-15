# 2장. 객체 생성과 파괴

## ✨ 아이템1. 생성자 대신 정적 팩터리 메서드를 고려하라.
***


#### ✔️ 이전 코드
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

#### ✔️ 변경 후 코드
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
#### ✔️ 이전 코드
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

#### ✔️ 변경 후 코드
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
#### ✔️ 이전 코드
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

#### ✔️ 변경 후 코드
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
#### ✔️ 이전 코드
```java
public class Computer{
  public Computer() {}

  public static void main(String[] args) {
    Computer computer = new Computer();
  }
}
```
<br>

#### ✔️ 변경 후 코드
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
#### ✔️ 이전 코드
```java
String s = new String("Effective");
```
<br>

#### ✔️ 변경 후 코드
```java
String s = "Effective";
```
<br>

#### ✔️ 코드리뷰 정리
이전 코드는 실행될 때마다 String 인스턴스를 새로 만든다. 반복문이나 빈번히 호출되는 메서드 안에 있다면 쓸데없는 인스턴스가 수많이 만들어질 수도 있다.  
이런 코드에 대해서는 변경 후 코드같이 하나의 String 인스턴스를 사용하자. 



## ✨ 아이템7. 다 쓴 객체 참조를 해제하라.
***
#### ✔️ 이전 코드
```java
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public Object pop() {
        if (size == 0) {
            throw new EmptyStackException();
        }
        return elements[--size];
    }

    private void ensureCapacity() {
        if (elements.length == size) {
            elements = Arrays.copyOf(elements, 2 * size + 1);
        }
    }
}
```
<br>

#### ✔️ 변경 후 코드
```java
public class Stack {

    // ...

    public Object pop() {
        if (size == 0) {
            throw new EmptyStackException();
        }
        Object result = elements[--size];
        elements[size] = null; // 다 쓴 참조 해제
        return result;
    }
}
```
<br>

#### ✔️ 코드리뷰 정리
이전코드에서는 프로그램 스택이 커졌다가 줄어들었을 때 스택에서 꺼내진 객체들을 가비지 컬렉터가 회수하지 않는다. 스택이 그 객체들의 다 쓴 참조를 여전히 가지고 있기 때문이다.  
하지만 변경 후 코드에서 수정된 pop 메소드는 다 쓴 참조를 null로 표현함으로써 해제 시켜주었다. 따라서 이 null 처리한 참조를 실수로 사용하려 하면 프로그램이 즉시 NullPointerException을 발생시켜 줘서 사전에 오류로 발견이 가능하게 되었다.


## ✨ 아이템8. finalizer와 cleaner 사용을 피하라.
***
#### ✔️ 이전 코드
```java
public class FinalizerExample {

    @Override
    protected final void finalize() throws Throwable {
        System.out.println("Clean up");
    }

    public void hello() {
        System.out.println("hi");
    }
}
```
```java
public class SampleRunner {

    public static void main(String[] args) {
      	SampleRunner runner = new SampleRunner();
        runner.run();
    }
    
    priavte void run() {
    	FinalizerExample finalizerExample = new FinalizerExample();
        finalizerExample.hello();    
    }
}
```
<br>

#### ✔️ 변경 후 코드
```java
public class SampleResource implements AutoCloseable{
    @Override
    public void close() throws Exception {
        System.out.println("close");
    }
    
    public void hello(){
        System.out.println("hello");
    }
}
```
```java
public class SampleRunner {

    public static void main(String[] args) throws Exception {
        SampleResource sampleResource = new SampleResource();
        sampleResource.hello();
        sampleResource.close();//자원 반납 필요시, 꼭 닫아줘야한다.
    }
}
```
<br>

#### ✔️ 코드리뷰 정리
이전 코드를 보면 finalizer 메소드가 GC의 대상은 되지만, GC가 바로 적용되는 것은 아니다. 그렇기에 finalizer 메소드가 언제 실행될지 모르고 실행이 안될수도 있다.  
그래서 변경 후 코드에서는 AutoCloseable 인터페이스를 구현하여 close 메소드를 사용하여 자원을 닫아주었다.




## ✨ 아이템9. try-finally 보다는 try-with-resources를 사용하라
***
#### ✔️ 이전 코드
```java
public class Copy {
    private static final int BUFFER_SIZE = 8 * 1024;
    
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
<br>

#### ✔️ 변경 후 코드
```java
public class Copy {
    private static final int BUFFER_SIZE = 8 * 1024;
    
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
<br>

#### ✔️ 코드리뷰 정리
이전코드의 경우 두번째 예외가 첫 번째 예외를 집어삼켜, 실제 시스템에서의 디버깅을 어렵게한다. 따라서 변경 후 코드처럼 try-with-resource를 사용하여 수정하였다. 훨씬 간결하다.
