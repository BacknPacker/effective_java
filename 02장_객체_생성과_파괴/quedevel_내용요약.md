# π₯ [ Chapter2 ] κ°μ²΄ μμ±κ³Ό νκ΄΄
## π―  μμ΄ν 1. μμ±μ λμ  μ μ  ν©ν°λ¦¬ λ©μλλ₯Ό κ³ λ €νλΌ.
ν΄λΌμ΄μΈνΈκ° ν΄λμ€μ μΈμ€ν΄μ€λ₯Ό μ»λ μ ν΅μ μΈ μλ¨μ public μμ±μλ€.<br>
νμ§λ§ λͺ¨λ  νλ‘κ·Έλλ¨Έκ° κΌ­ μμλ¬μΌ ν  κΈ°λ²μ΄ νλ λ μλ€.<br>
ν΄λμ€λ μμ±μμ λ³λλ‘ μ μ  ν©ν°λ¦¬ λ©μλλ₯Ό μ κ³΅ν  μ μλ€.
> β μ μ  ν©ν°λ¦¬ λ©μλλ λμμΈ ν¨ν΄μμμ ν©ν°λ¦¬ λ©μλμ λ€λ₯΄λ€.
### π μ₯μ 
#### 1οΈβ£ μ΄λ¦μ κ°μ§ μ μλ€.
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
+ κ°μ²΄μ νΉμ±μ μ½κ² λ¬μ¬ν  μ μλ€.
#### 2οΈβ£ νΈμΆλ  λλ§λ€ μΈμ€ν΄μ€λ₯Ό μλ‘ μμ±νμ§λ μμλ λλ€.
```java
public static Boolean valueOf(boolean bololean){
  return b Boolean.TRUE : Boolean.FALSE;
}
```
+ μΈμ€ν΄μ€λ₯Ό λ―Έλ¦¬ λ§λ€μ΄ λκ±°λ μλ‘ μμ±ν μΈμ€ν΄μ€λ₯Ό μΊμ±νμ¬ μ¬νμ©νλμμΌλ‘ λΆνμν κ°μ²΄ μμ±μ νΌν  μ μλ€.
#### 3οΈβ£ λ°ν νμμ νμ νμ κ°μ²΄λ₯Ό λ°νν  μ μλ λ₯λ ₯μ΄ μλ€.
<img width="80%" src="https://user-images.githubusercontent.com/55771326/163700075-ec458800-467e-4242-9903-6ac31b927b46.png"/>

+ λ°νν  κ°μ²΄μ ν΄λμ€λ₯Ό μμ λ‘­κ² μ νν  μ μκ²νλ μμ²­λ μ μ°μ±μ μ κ³΅νλ€.
+ λ°ννλ νμμ μΈν°νμ΄μ€ νμμΌλ‘ ν  κ²½μ°, ν΄λΉ μΈν°νμ΄μ€λ₯Ό κ΅¬ννλ
  λ€λ₯Έ νμμ κ°μ²΄λ€μ λ°νν  μ μμμ λ§νλ€. (μμ)
+ μ μ  ν©ν°λ¦¬λ₯Ό μ¬μ©νλ ν΄λΌμ΄μΈνΈλ μ»μ κ°μ²΄λ₯Ό μΈν°νμ΄μ€λ§μΌλ‘ λ€λ£¨κ² λλ€.

#### 4οΈβ£ μλ ₯ λ§€κ°λ³μμ λ°λΌ λ§€λ² λ€λ₯Έ ν΄λμ€μ κ°μ²΄λ₯Ό λ°νν  μ μλ€.
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

+ μμλ‘ EnumSet ν΄λμ€λ publi μμ±μ μμ΄ μ€μ§ μ μ  ν©ν°λ¦¬ λ©μλλ§ μ κ³΅νλ€.
+ λ§μ½ μμκ° 64κ° μ΄νμ΄λ©΄ μμλ€μ long λ³μ νλλ‘ κ΄λ¦¬νλ RegularEnumSetμ μΈμ€ν΄μ€
+ 65κ° μ΄μμ΄λ©΄ long λ°°μ΄λ‘ κ΄λ¦¬νλ JunboEnumSetμ μΈμ€ν΄μ€λ₯Ό λ°ννλ€.
+ ν΄λΌμ΄μΈνΈλ μ΄ λ ν΄λμ€μ μ‘΄μ¬λ₯Ό λͺ°λΌλ λλ€.

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

#### 5οΈβ£ μ μ  ν©ν°λ¦¬ λ©μλλ₯Ό μμ±νλ μμ μλ λ°νν  κ°μ²΄μ ν΄λμ€κ° μ‘΄μ¬νμ§ μμλ λλ€.

### π λ¨μ 
#### 1οΈβ£ μμμ νλ €λ©΄ publicμ΄λ protected μμ±μκ° νμνλ μ μ  ν©ν°λ¦¬ λ©μλλ§ μ κ³΅νλ©΄ νμ ν΄λμ€λ₯Ό λ§λ€ μ μλ€.
+ μ»¬λ μ νλ μμν¬μ μ νΈλ¦¬ν° κ΅¬ν ν΄λμ€λ€μ μμν  μ μλ€
#### 2οΈβ£ μ μ  ν©ν°λ¦¬ λ©μλλ νλ‘κ·Έλλ¨Έκ° μ°ΎκΈ° μ΄λ ΅λ€.
+ μμ±μμ²λΌ API μ€λͺμ λͺνν λλ¬λμ§ μμΌλ μμ©μλ μ μ  ν©ν°λ¦¬ λ©μλ λ°©μ ν΄λμ€λ₯Ό μΈμ€ν΄νν  λ°©λ²μ μμλ΄μΌ νλ€.


## π―  μμ΄ν 2. μμ±μμ λ§€κ°λ³μκ° λ§λ€λ©΄ λΉλλ₯Ό κ³ λ €νλΌ.

### if. μμ±μμ λ§€κ°λ³μκ° λ§λ€λ©΄ β
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
+ μ μΈ΅μ  μμ±μ ν¨ν΄λ μΈ μλ μμ§λ§, λ§€κ°λ³μ κ°μκ° λ§μμ§λ©΄ ν΄λΌμ΄μΈνΈ μ½λλ₯Ό μμ±νκ±°λ μ½κΈ° μ΄λ ΅λ€.

### λμ β
#### 1οΈβ£ μλ°λΉμ¦ ν¨ν΄
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
+ μλ°λΉμ¦ ν¨ν΄μμλ κ°μ²΄ νλλ₯Ό λ§λ€λ €λ©΄ λ©μλλ₯Ό μ¬λ¬ κ° νΈμΆν΄μΌ νκ³ , κ°μ²΄κ° μμ ν μμ±λκΈ° μ κΉμ§λ μΌκ΄μ±μ΄ λ¬΄λμ§ μνμ λμ΄κ² λλ€.

#### 2οΈβ£ λΉλ ν¨ν΄
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
+ λΉλλ μ μΈ΅μ  μμ±μλ³΄λ€ ν΄λΌμ΄μΈνΈ μ½λλ₯Ό μ½κ³  μ°κΈ°κ° ν¨μ¬ κ°κ²°νκ³ , μλ°λΉμ¦λ³΄λ€ ν¨μ  μμ νλ€.

## π―  μμ΄ν 3. private μμ±μλ μ΄κ±° νμμΌλ‘ μ±κΈν΄μμ λ³΄μ¦νλΌ.
```java
class Singleton {
    // LazyHolder Singleton pattern
    private Singleton(){};
    private static class LazyHolder{
        public static final Singleton instance = new Singleton();
    }
    // getInstance()λ₯Ό νΈμΆνμλ LazyHolder ν΄λμ€κ° λ‘λ©λλ©΄μ μμ±
    // μ₯μ  : κ°μ²΄κ° νμν μμ μμ μ΄κΈ°νκ° μ§νλλ€.
    public static Singleton getInstance(){
        return LazyHolder.instance;
    }
}
```
+ κ°μ²΄λ₯Ό μ€μ§ νλλ§ μμ±νμ¬ νμ©νλκ²μ μ±κΈν΄μ΄λΌκ³  νλ€.
+ μ±κΈν΄μ λ§λ€λ λ°λμ κ³ λ €ν΄μΌνλ μ¬ν­μ λ¬΄μν κ°μ²΄μ΄λ€.

## π―  μμ΄ν 4. μΈμ€ν΄μ€νλ₯Ό λ§μΌλ €κ±°λ  private μμ±μλ₯Ό μ¬μ©νλΌ.
+ κ°μ²΄ μ§ν₯μ μΌλ‘ μ¬κ³ νμ§ μλ μ΄λ€μ΄ μ’μ’ λ¨μ©νλ λ°©μμ΄μ§λ§ λλ¦μ μ°μμκ° μμΌλ κ³ λ €ν΄μ μ νμ©νμ
+ Util ν΄λμ€μ²λΌ μ­ν μ΄ λΆλͺνκ² μ‘΄μ¬νμ¬ μ€κ³λ ν΄λμ€λ privateμΌλ‘ μΈμ€ν΄μ€ μμ±μ λ°©μ΄νμ.

## π―  μμ΄ν 5. μμμ μ§μ  λͺμνμ§ λ§κ³  μμ‘΄ κ°μ²΄ μ£Όμμ μ¬μ©νλΌ.
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
+ `SpellChecker`μμ dictionary λ³μλ₯Ό μμ±μλ₯Ό ν΅ν΄μ μΈλΆμμ μ£Όμλ°κ³ λ‘ μμμ΄ λμ΄μλ€.
+ μ΄λ νμν μμμ μμ±μμ λκ²¨μ£Όμ΄ ν΄λμ€μ μ μ°μ±κ³Ό μ¬μ¬μ©μ±μ κ°μ ν΄μ€λ€.

## π―  μμ΄ν 6. λΆνμν κ°μ²΄ μμ±μ νΌνλΌ
```java
public static void main(String[] args) {
    // λμ°νκ² λλ¦° λ°μ±νμ κ³μ°
    Long sum = 0L;
    for (long i = 0; i < Integer.MAX_VALUE; i++) {
        sum += i;
    }
    System.out.println(sum);
}
```
+ μμ± λΉμ©μ΄ μμ£Ό λΉμΌ κ°μ²΄κ° λ°λ³΅ν΄μ νμνλ€λ©΄ μΊμ±νμ¬ μ¬μ¬μ©νκΈΈ κΆμ₯
+ μ€ν λ°μ±μ κΈ°λ³Έ νμκ³Ό κ·Έμ λμνλ λ°μ±λ κΈ°λ³Έ νμμ κ΅¬λΆμ νλ €μ£Όμ§λ§, μμ ν μμ μ£Όλ κ²μ μλλ€.
+ λ°μ±λ κΈ°λ³Έ νμλ³΄λ€λ κΈ°λ³Έ νμμ μ¬μ©νκ³ , μλμΉ μμ μ€ν λ°μ±μ΄ μ¨μ΄λ€μ§ μλλ‘ μ£Όμνμ.

## π―  μμ΄ν 7. λ€ μ΄ κ°μ²΄ μ°Έμ‘°λ₯Ό ν΄μ νλΌ.

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
μλ `Stack` ν΄λμ€μ μΌλΆλΆμ΄λ€.<br>
μ€νμ΄ μ»€μ‘λ€κ° μ€μ΄λ€μμ λ μ€νμμ κΊΌλ΄μ§ κ°μ²΄λ€μ κ°λΉμ§ μ»¬λ ν°κ° νμνμ§ μλλ€.<br>
νλ‘κ·Έλ¨μμ κ·Έ κ°μ²΄λ€μ λ μ΄μ μ¬μ©νμ§ μλλΌλ λ§μ΄λ€.

```java
// μ λλ‘ κ΅¬νν popλ©μλ
public Object pop() {
  if (size == 0) {
    throw new EmptyStackException();
  }
  Ojbect result = elements[--size];
  elements[size] = null;
  return result;
}
```
+ κ°μ²΄ μ°Έμ‘°λ₯Ό null μ²λ¦¬νλ μΌμ μμΈμ μΈ κ²½μ°μ¬μΌ νλ€. ( νλ‘κ·Έλ¨μ νμ μ΄μμΌλ‘ μ§μ λΆνκ² λ§λ€κΈ° λλ¬Έ )
+ μκΈ° λ©λͺ¨λ¦¬λ₯Ό μ§μ  κ΄λ¦¬νλ ν΄λμ€λΌλ©΄ νλ‘κ·Έλλ¨Έλ ν­μ λ©λͺ¨λ¦¬ λμμ μ£Όμν΄μΌ νλ€.
+ μΊμ μ­μ λ©λͺ¨λ¦¬ λμλ₯Ό μΌμΌν€λ μ£Όλ²μ΄λ€.

## π―  μμ΄ν 8. finalizerμ cleaner μ¬μ©μ νΌνλΌ
+ finalizerλ μμΈ‘ν  μ μκ³ , μν©μ λ°λΌ μνν  μ μμ΄ μΌλ°μ μΌλ‘ λΆνμνλ€.
+ cleanerλ finalizerλ³΄λ€λ λ μννμ§λ§, μ¬μ ν μμΈ‘ν  μ μκ³ , λλ¦¬κ³ , μΌλ°μ μΌλ‘ λΆνμνλ€.
+ μ¦, finalizerμ cleanerλ‘λ μ λ μ€νλμ΄μΌ νλ μμμ μ λ ν  μ μλ€.
+ μνλ₯Ό μκ΅¬μ μΌλ‘ μμ νλ μμμμλ μ λ finalizerμ cleanerμ μμ‘΄ν΄μλ μλλ€.
+ finalizerμ cleanerλ μ¬κ°ν μ±λ₯ λ¬Έμ λ λλ°νλ€.
+ finalizerλ₯Ό μ¬μ©ν ν΄λμ€λ finalizer κ³΅κ²©μ λΈμΆλμ΄ μ¬κ°ν λ³΄μ λ¬Έμ λ₯Ό μΌμΌν¬ μ μλ€.
> μ°μ§λ§λΌλ λ§μ μ₯ν©νκ² μ¨μλκ±Έλ³΄λ©΄ μ λλ‘ μ°λ©΄ μλ κ² κ°λ€...β

## π―  μμ΄ν 9. try-finallyλ³΄λ€λ try-with-resourcesλ₯Ό μ¬μ©νλΌ.
+ close λ©μλλ₯Ό μ§μ  νΈμΆνμ¬ λ«μμ£Όλ μμλ€μ΄ μ‘΄μ¬νκΈ° λλ¬Έμ ν΄λΌμ΄μΈνΈκ° λμΉκΈ°κ° μ½λ€.
+ μμ λ§μΌλ‘ finalizerλ₯Ό νμ©νκ³  μμ§λ§ [μμ΄ν8]μμ λ§νλ― λ―Ώμκ² λͺ»λλ€.

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
+ μμΈλ try λΈλ‘κ³Ό finally λΈλ‘ λͺ¨λ λ°μν  μ μλ€.
+ μ€ν μΆμ  λ΄μ­μ μ²« λ²μ§Έ μμΈμ κ΄ν μ λ³΄λ λ¨μ§ μκ² λμ΄, μ€μ  μμ€νμμμ λλ²κΉμ λͺΈμ μ΄λ ΅κ² νλ€.
+ μ½λκ° λλ¬΄ μ§μ λΆνλ€.

```java
// λ³΅μμ μμμ μ²λ¦¬νλ try-with-resources μ§§κ³  λ§€νΉμ μ΄λ€ !
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
+ try-finallyλ‘ μμ±νλ©΄ μ€μ©μ μ΄μ§ λͺ»ν  λ§νΌ μ½λκ° μ§μ λΆν΄μ§λ κ²½μ°λΌλ, try-with-resourcesλ‘λ μ ννκ³  μ½κ² μμμ νμν  μ μλ€.

## β­ κ²°λ‘ 
λΆνμν μμ μμ±μ νΌνκ³ , λ©λͺ¨λ¦¬ λμ, μμ νμ λ±μ λν μλ°©λ²μ νμ©νμ!


<br>

> Joshua Bloch, γEffective Java 3/Eγ, κ°μλ§΅μ μ?κΉ, νλ‘κ·Έλλ°μΈμ¬μ΄νΈ(2018), p8-50.