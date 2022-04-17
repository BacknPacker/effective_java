# 🔥 [ Chapter2 ] 객체 생성과 파괴
## 🎯  아이템 1. 생성자 대신 정적 팩터리 메서드를 고려하라.
클라이언트가 클래스의 인스턴스를 얻는 전통적인 수단은 public 생성자다.<br>
하지만 모든 프로그래머가 꼭 알아둬야 할 기법이 하나 더 있다.<br>
클래스는 생성자와 별도로 정적 팩터리 메서드를 제공할 수 있다.
> ✅ 정적 팩터리 메서드는 디자인 패턴에서의 팩터리 메서드와 다르다.
### 👍 장점
#### 1️⃣ 이름을 가질 수 있다.
```java
class Member {
  private String mbrId;
  private String mbrPw;

  public static Member ofMemberIdAndMbrPw(String mbrId, String mbrPw) {
    Member member = new Member();
    member.setMbrId(mbrId);
    member.setMbrPw(mbrPw);
    return member;
  }
}
```
+ 객체의 특성을 쉽게 묘사할 수 있다.
#### 2️⃣ 호출될 때마다 인스턴스를 새로 생성하지는 않아도 된다.
```java
public static Boolean valueOf(boolean bololean){
        return b Boolean.TRUE : Boolean.FALSE;
        }
```
+ 인스턴스를 미리 만들어 놓거나 새로 생성한 인스턴스를 캐싱하여 재활용하는식으로 불필요한 객체 생성을 피할 수 있다.
#### 3️⃣ 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.
<img width="80%" src="https://user-images.githubusercontent.com/55771326/163700075-ec458800-467e-4242-9903-6ac31b927b46.png"/>

+ 반환할 객체의 클래스를 자유롭게 선택할 수 있게하는 엄청난 유연성을 제공한다.
+ 반환하는 타입을 인터페이스 타입으로 할 경우, 해당 인터페이스를 구현하는
  다른 타입의 객체들을 반환할 수 있음을 말한다. (상속)
+ 정적 팩터리를 사용하는 클라이언트는 얻은 객체를 인터페이스만으로 다루게 된다.

#### 4️⃣ 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.
```java
/**
 * Creates an empty enum set with the specified element type.
 *
 * @param <E> The class of the elements in the set
 * @param elementType the class object of the element type for this enum
 *     set
 * @return An empty enum set of the specified type.
 * @throws NullPointerException if {@code elementType} is null
 */
public static <E extends Enum<E>> EnumSet<E> noneOf(Class<E> elementType) {
    Enum<?>[] universe = getUniverse(elementType);
    if (universe == null)
        throw new ClassCastException(elementType + " not an enum");

    if (universe.length <= 64)
        return new RegularEnumSet<>(elementType, universe);
    else
        return new JumboEnumSet<>(elementType, universe);
}
```

+ 예시로 EnumSet 클래스는 publi 생성자 없이 오직 정적 팩터리 메서드만 제공한다.
+ 만약 원소가 64개 이하이면 원소들은 long 변수 하나로 관리하는 RegularEnumSet의 인스턴스
+ 65개 이상이면 long 배열로 관리하는 JunboEnumSet의 인스턴스를 반환한다.
+ 클라이언트는 이 두 클래스의 존재를 몰라도 된다.

```java
public abstract class Discount {
  public static <E extends Discount> Discount getInstance(String code){
    return code.equals("1")? new RateDiscount() : new FixedDiscount();
  }
}
public class FixedDiscount extends Discount{
  public FixedDiscount() {
    System.out.println("FixedDiscount.FixedDiscount");
  }
}
public class RateDiscount extends Discount{
  public RateDiscount() {
    System.out.println("RateDiscount.RateDiscount");
  }
}
```

#### 5️⃣ 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.

### 👎 단점
#### 1️⃣ 상속을 하려면 public이나 protected 생성자가 필요하니 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없다.
+ 컬렉션 프레임워크의 유틸리티 구현 클래스들은 상속할 수 없다
#### 2️⃣ 정적 팩터리 메서드는 프로그래머가 찾기 어렵다.
+ 생성자처럼 API 설명에 명확히 드러나지 않으니 샂용자는 정적 팩터리 메서드 방식 클래스를 인스턴화할 방법을 알아내야 한다.


## 🎯  아이템 2. 생성자에 매개변수가 많다면 빌더를 고려하라.

### if. 생성자의 매개변수가 많다면 ❓
```java
public class User {

    private long userSn;
    private String userId;
    private String userPw;

    public User(long userSn) {
        this.userSn = userSn;
    }

    public User(long userSn, String userId) {
        this.userSn = userSn;
        this.userId = userId;
    }

    public User(long userSn, String userId, String userPw) {
        this.userSn = userSn;
        this.userId = userId;
        this.userPw = userPw;
    }
}
```
+ 점층적 생성자 패턴도 쓸 수는 있지만, 매개변수 개수가 많아지면 클라이언트 코드를 작성하거나 읽기 어렵다.

### 대안 ❗
#### 1️⃣ 자바빈즈 패턴
```java
public class User {

    private long userSn;
    private String userId;
    private String userPw;

    public void setUserSn(long userSn) {
        this.userSn = userSn;
    }

    public void setUserId(String userId) {
        this.userId = userId;
    }

    public void setUserPw(String userPw) {
        this.userPw = userPw;
    }
}
```
+ 자바빈즈 패턴에서는 객체 하나를 만들려면 메서드를 여러 개 호출해야 하고, 객체가 완전히 생성되기 전까지는 일관성이 무너진 상태에 놓이게 된다.

#### 2️⃣ 빌더 패턴
```java
public class User {

    private long userSn;
    private String userId;
    private String userPw;

    public User(Builder builder){
        this.userSn = builder.userSn;
        this.userId = builder.userId;
        this.userPw = builder.userPw;
    }

    public static class Builder {
        public long userSn;
        public String userId;
        public String userPw;

        private Builder() {
        }

        public Builder with(Consumer<Builder> consumer){
            consumer.accept(this);
            return this;
        }

        public User build(){
            return new User(this);
        }
    }
}
```
+ 빌더는 점층적 생성자보다 클라이언트 코드를 읽고 쓰기가 훨씬 간결하고, 자바빈즈보다 훨신 안전하다.

## 🎯  아이템 3. private 생성자나 열거 타입으로 싱글턴임을 보증하라.
```java
class Singleton {
    // LazyHolder Singleton pattern
    private Singleton(){};
    private static class LazyHolder{
        public static final Singleton instance = new Singleton();
    }
    // getInstance()를 호출했을때 LazyHolder 클래스가 로딩되면서 생성
    // 장점 : 객체가 필요한 시점에서 초기화가 진행된다.
    public static Singleton getInstance(){
        return LazyHolder.instance;
    }
}
```
+ 객체를 오직 하나만 생성하여 활용하는것을 싱글턴이라고 한다.
+ 싱글턴을 만들때 반드시 고려해야하는 사항은 무상태 객체이다.

## 🎯  아이템 4. 인스턴스화를 막으려거든 private 생성자를 사용하라.
+ 객체 지향적으로 사고하지 않는 이들이 종종 남용하는 방식이지만 나름에 쓰임새가 있으니 고려해서 잘 활용하자
+ Util 클래스처럼 역할이 분명하게 존재하여 설계된 클래스는 private으로 인스턴스 생성은 방어하자.

## 🎯  아이템 5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라.
```java
abstract class Lexicon { ... }
class EnglishLexicon extends Lexicon { ... }
class KoreanLexicon extends Lexicon { ... }
class SpellChecker {
    private Lexicon dictionary;
    public SpellChecker(Supplier<? extends Lexicon> supplier){
        this.dictionary = supplier.get();
    }
    public static boolean isValid(String word){ ... }
}
```
+ `SpellChecker`에서 dictionary 변수를 생성자를 통해서 외부에서 주입받고록 작업이 되어있다.
+ 이는 필요한 자원을 생성자에 넘겨주어 클래스의 유연성과 재사용성을 개선해준다.

## 🎯  아이템 6. 불필요한 객체 생성을 피하라
```java
public static void main(String[] args) {
    // 끔찍하게 느린 박싱타입 계산
    Long sum = 0L;
    for (long i = 0; i < Integer.MAX_VALUE; i++) {
        sum += i;
    }
    System.out.println(sum);
}
```
+ 생성 비용이 아주 비싼 객체가 반복해서 필요하다면 캐싱하여 재사용하길 권장
+ 오토박싱은 기본 타입과 그에 대응하는 박싱된 기본 타입의 구분을 흐려주지만, 완전히 없애주는 것은 아니다.
+ 박싱된 기본 타입보다는 기본 타입을 사용하고, 의도치 않은 오토박싱이 숨어들지 않도록 주의하자.

## 🎯  아이템 7. 다 쓴 객체 참조를 해제하라.

```java
import java.util.EmptyStackException;

public class Stack {
  private Object[] elements;
  private int size = 0;
    
  public Object pop() {
    if (size == 0) {
      throw new EmptyStackException();
    }
    return elements[--size];
  }
}
```
위는 `Stack` 클래스의 일부분이다.<br>
스택이 커졌다가 줄어들었을 때 스택에서 꺼내진 객체들을 가비지 컬렉터가 회수하지 않는다.<br>
프로그램에서 그 객체들을 더 이상 사용하지 않더라도 말이다.

```java
// 제대로 구한한 pop메서드
public Object pop() {
  if (size == 0) {
    throw new EmptyStackException();
  }
  Ojbect result = elements[--size];
  elements[size] = null;
  return result;
}
```
+ 객체 참조를 null 처리하는 일은 예외적인 경우여야 한다. ( 프로그램을 필요 이상으로 지저분하게 만들기 때문 )
+ 자기 메모리를 직접 관리하는 클래스라면 프로그래머는 항시 메모리 누수에 주의해야 한다.
+ 캐시 역시 메모리 누수를 일으키는 주범이다.

## 🎯  아이템 8. finalizer와 cleaner 사용을 피하라
+ finalizer는 예측할 수 없고, 상황에 따라 위험할 수 있어 일반적으로 불필요하다.
+ cleaner는 finalizer보다는 덜 위험하지만, 여전히 예측할 수 없고, 느리고, 일반적으로 불필요하다.
+ 즉, finalizer와 cleaner로는 제때 실행되어야 하는 작업은 절대 할 수 없다.
+ 상태를 영구적으로 수정하는 작업에서는 절대 finalizer와 cleaner에 의존해서는 안된다.
+ finalizer와 cleaner는 심각한 성능 문제도 동반한다.
+ finalizer를 사용한 클래스는 finalizer 공격에 노출되어 심각한 보안 문제를 일으킬 수 있다.
> 쓰지말라는 말을 장황하게 써있는걸보면 절대로 쓰면 안될것 같다...❗

## 🎯  아이템 9. try-finally보다는 try-with-resources를 사용하라.
+ close 메서드를 직접 호출하여 닫아주는 자원들이 존재하기 때문에 클라이언트가 놓치기가 쉽다.
+ 안전망으로 finalizer를 활용하고 있지만 [아이템8]에서 말했듯 믿을게 못된다.

```java
static void copy(String src, String dst) throws IOException {
    InputStream in = new FileInputStream(src);
    try {
        OutputStream out = new FileOutputStream(dst);
        try {
            byte[] buf = new byte[BUFFER_SIZE];
            int n;
            while ((n=in.read(buf)) >= 0){
                out.write(buf,0,n);
            }
        } finally {
            out.close();
        }
    } finally {
        in.close();
    }
}
```
+ 예외는 try 블록과 finally 블록 모두 발생할 수 있다.
+ 스택 추적 내역에 첫 번째 예외에 관한 정보는 남지 않게 되어, 실제 시스템에서의 디버깅을 몸시 어렵게 한다.
+ 코드가 너무 지저분하다.

```java
// 복수의 자원을 처리하는 try-with-resources 짧고 매혹적이다 !
static void copy(String src, String dst) throws IOException {
  try (InputStream in = new FileInputStream(src);
    OutputStream out = new FileOutputStream(dst);){
    byte[] buf = new byte[BUFFER_SIZE];
    int n;
    while ((n=in.read(buf)) >= 0){
        out.write(buf,0,n);
    }
  }
}
```
+ try-finally로 작성하면 실용적이지 못할 만큼 코드가 지저분해지는 경우라도, try-with-resources로는 정확하고 쉽게 자원을 회수할 수 있다.

## ⭐ 결론
불필요한 자원 생성을 피하고, 메모리 누수, 자원 회수 등에 대한 예방법을 활용하자!