# π₯ [ Chapter8 ] λ©μλ

## π―  μμ΄ν 49. λ§€κ°λ³μκ° μ ν¨νμ§ κ²μ¬νλΌ.
#### "μ€λ₯λ κ°λ₯ν ν λΉ¨λ¦¬ (λ°μν κ³³μμ) μ‘μμΌ νλ€" <br>
μ€λ₯λ₯Ό λ°μν μ¦μ μ‘μ§ λͺ»νλ©΄ ν΄λΉ μ€λ₯λ₯Ό κ°μ§νκΈ° μ΄λ €μμ§κ³ , κ°μ§νλλΌλ μ€λ₯μ λ°μ μ§μ μ μ°ΎκΈ° μ΄λ €μ μ§λ€.<br>
<br>

#### λ§μ½, λ§€κ°λ³μ κ²μ¬λ₯Ό μ λλ‘ νμ§ λͺ»νλ©΄ λͺ κ°μ§ λ¬Έμ κ° μκΈΈ μ μλ€.
λ©μλκ° μνλλ μ€κ°μ λͺ¨νΈν μμΈλ₯Ό λμ§λ©° μ€ν¨ν  μ μλ€. <br>
λ§€κ°λ³μ κ²μ¬μ μ€ν¨νλ©΄ **μ€ν¨ μμμ±**μ μ΄κΈ°λ κ²°κ³Όλ₯Ό λ³μ μ μλ€.<br>
**_μ€ν¨ μμμ± : λ©μλμμ μμΈκ° λ°μν νμλ κ·Έ κ°μ²΄λ μ¬μ ν μ ν¨ν μν_**<br>

`public`κ³Ό `protected` λ©μλλ λ§€κ°λ³μ κ°μ΄ μλͺ»λμ λ λμ§λ μμΈλ₯Ό λ¬Έμνν΄μΌ νλ€.<br>
(`@throws` μλ°λ νκ·Έλ₯Ό μ¬μ©νλ©΄ λλ€.)<br>
```java
    /**
     * (νμ¬ κ° mod m) κ°μ λ°ννλ€. μ΄ λ©μλλ
     * ν­μ μμ΄ μλ BigIntegerλ₯Ό λ°ννλ€λ μ μμ remainder λ©μλμ λ€λ₯΄λ€.
     * 
     * @param m κ³μ (μμμ¬μΌ νλ€.)
     * @return νμ¬ κ° mod m
     * @throws ArithmeticException mμ΄ 0λ³΄λ€ μκ±°λ κ°μΌλ©΄ λ°μνλ€.
     */
    public BigInteger mod(BigInteger m){
        if (m.signum() <= 0){
            throw new ArithmeticException("κ³μ (m)λ μμμ¬μΌ ν©λλ€. "+m);
        }
        // ,,, κ³μ° μν
    }
```
μ¬κΈ°μ NullPointException(NPE)μ λν λ΄μ©μ κΈ°μ λμ΄ μμ§ μμ§λ§,<br>
BigInteger ν΄λμ€ μμ€μμ κΈ°μ νκΈ° λλ¬Έμ μ΄ ν΄λμ€μ public λ©μλ μ μ²΄μ μ μ©λκΈ°μ κ° λ©μλμ κΈ°μ  ν  νμκ° μλ€.<br>
<br>
#### java.util.Objects.requireNonNull<br>
μλ° 7μ μΆκ°λ λ©μλλ‘ μ μ°νκ³  μ¬μ©νκΈ°λ νΈνλ, λ μ΄μ null κ²μ¬λ₯Ό μλμΌλ‘ νμ§ μμλ λλ€.<br>
```java
public static <T> T requireNonNull(T obj) {
    if (obj == null)
        throw new NullPointerException();
    return obj;
}

public static <T> T requireNonNull(T obj, String message) {
    if (obj == null)
        throw new NullPointerException(message);
    return obj;
}
```
<br>

#### publicμ΄ μλ λ©μλλΌλ©΄ λ¨μΈλ¬Έ(assert)μ μ¬μ©ν΄ λ§€κ°λ³μ μ ν¨μ±μ κ²μ¦ν  μ μλ€.
```java
public class Item49 {
    private static void solution(int[] arr, int cnt){
        assert arr != null;
        assert cnt > 0;
        System.out.println("---------");
        System.out.println("arr.length = " + arr.length);
        System.out.println("cnt = " + cnt);
        System.out.println("---------");
    }

    public static void main(String[] args) {
        solution(null, -1);
    }
}
```
<br>
<img width="40%" src="https://user-images.githubusercontent.com/55771326/170809534-41dae2d0-030a-487b-8a15-a07eb819068b.png">
<br>
<img width="40%" src="https://user-images.githubusercontent.com/55771326/170809504-ca6da5e9-14ca-4cae-b86a-bdeab35b83b6.png">
<br>

λ©μλλ μμ±μλ₯Ό μμ±ν  λλ©΄ κ·Έ λ§€κ°λ³μλ€μ μ΄λ€ μ μ½μ΄ μμμ§ μκ°ν΄μΌ νλ€.<br>
κ·Έ μ μ½λ€μ λ¬Έμννκ³  λ©μλ μ½λ μμ λΆλΆμμ λͺμμ μΌλ‘ κ²μ¬ν΄μΌνλ€.<br>

## π―  μμ΄ν 50. μ μμ λ°©μ΄μ  λ³΅μ¬λ³Έμ λ§λ€λΌ.
#### ν΄λΌμ΄μΈνΈκ° μ¬λ¬λΆμ λΆλ³μμ κΉ¨λ¨λ¦¬λ € νμμ΄ λμ΄ μλ€κ³  κ°μ νκ³  λ°©μ΄μ μΌλ‘ νλ‘κ·Έλλ°ν΄μΌ νλ€.
<br>
* κΈ°κ°μ νννλ ν΄λμ€ - λΆλ³μμ μ§ν€μ§ λͺ»νλ€.<br>

```java
public final class Period {
    private final Date start;
    private final Date end;

    /**
     * @param  start μμ μκ°
     * @param  end μ’λ£ μκ°. μμ μκ°λ³΄λ€ λ€μ¬μΌ νλ€.
     * @throws IllegalArgumentException μμ μκ°μ΄ μ’λ£ μκ°λ³΄λ€ λ¦μ λ λ°μνλ€.
     * @throws NullPointerException startλ endκ° nullμ΄λ©΄ λ°μνλ€.
     */
    public Period(Date start, Date end) {
        if (start.compareTo(end) > 0)
            throw new IllegalArgumentException(
                    start + "κ° " + end + "λ³΄λ€ λ¦λ€.");
        this.start = start;
        this.end = end;
    }

    public Date start() {
        return start;
    }

    public Date end() {
        return end;
    }

    public String toString() {
        return start + " - " + end;
    }
}    
```
μΌν μ΄ ν΄λμ€λ λΆλ³μ²λΌ λ³΄μ΄κ³ , μμ μκ°μ΄ μ’λ£ μκ°λ³΄λ€ λ¦μ μ μλ€λ λΆλ³μμ΄ λ¬΄λ¦¬ μμ΄ μ§μΌμ§ κ² κ°λ€. <br>
νμ§λ§ Dateκ° κ°λ³μ΄λΌλ μ¬μ€μ μ΄μ©νλ©΄ μ΄λ ΅μ§ μκ² κ·Έ λΆλ³μμ κΉ¨λ¨λ¦΄ μ μλ€.<br>
<br>
* Period μΈμ€ν΄μ€μ λ΄λΆλ₯Ό κ³΅κ²©ν΄λ³΄μ.<br>
```java
Date start = new Date();
Date end = new Date();
Period p = new Period(start,end);
end.setYear(78); // pμ λ΄λΆλ₯Ό μμ νλ€.
```
**Dateλ λ‘μ APIμ΄λ μλ‘μ΄ μ½λλ₯Ό μμ±ν  λλ λ μ΄μ μ¬μ©νλ©΄ μλλ€.**<br>
<br>
μΈλΆ κ³΅κ²©μΌλ‘λΆν° Period μΈμ€ν΄μ€μ λ΄λΆλ₯Ό λ³΄νΈνλ €λ©΄ **μμ±μμμ λ°μ κ°λ³ λ§€κ°λ³μ κ°κ°μ λ°©μ΄μ μΌλ‘ λ³΅μ¬ν΄μΌνλ€.**<br>
* μμ ν μμ±μ - λ§€κ°λ³μμ λ°©μ΄μ  λ³΅μ¬λ³Έμ λ§λ λ€.<br>
```java
public Period(Date start, Date end) {
    this.start = new Date(start.getTime());
    this.end   = new Date(end.getTime());

    if (this.start.compareTo(this.end) > 0)
        throw new IllegalArgumentException(
                this.start + "κ° " + this.end + "λ³΄λ€ λ¦λ€.");
}
```
λ§€κ°λ³μμ μ ν¨μ±μ κ²μ¬νκΈ° μ μ λ°©μ΄μ  λ³΅μ¬λ³Έμ λ§λ€κ³ , μ΄ λ³Έμ¬λ³ΈμΌλ‘ μ ν¨μ±μ κ²μ¬ν μ μ μ£Όλͺ©νμ.<br>
<br>

μμ±μλ₯Ό μμ νλ©΄ μμμ κ³΅κ²©μ λ§μλΌ μ μμ§λ§, Period μΈμ€ν΄μ€λ μμ§λ λ³κ²½μ΄ κ°λ₯νλ€.<br>
μ κ·Όμ λ©μλκ° λ΄λΆμ κ°λ³ μ λ³΄λ₯Ό μ§μ  λλ¬λ΄κΈ° λλ¬Έμ΄λ€.<br>
* μμ ν μ κ·Όμ - νλμ λ°©μ΄μ  λ³΅μ¬λ³Έμ λ°ννλ€.<br>
```java
public Date start() {
    return new Date(start.getTime());
}

public Date end() {
    return new Date(end.getTime());
}
```
<br>
ν΄λμ€κ° ν΄λΌμ΄μΈνΈλ‘λΆν° λ°λ νΉμ ν΄λΌμ΄μΈνΈλ‘ λ°ννλ κ΅¬μ±μμκ° κ°λ³μ΄λΌλ©΄<br>
κ·Έ μμλ λ°λμ λ°©μ΄μ μΌλ‘ λ³΅μ¬ν΄μΌνλ€.<br>

## π―  μμ΄ν 51. λ©μλ μκ·Έλμ²λ₯Ό μ μ€ν μ€κ³νλΌ.
#### 1. λ©μλ μ΄λ¦μ μ μ€μ΄ μ§μ.
ν­μ νμ€ λͺλͺ κ·μΉμ λ°λΌμΌ νλ€. μ΄ν΄ν  μ μκ³ , κ°μ ν¨ν€μ§μ μν λ€λ₯Έ μ΄λ¦λ€κ³Ό μΌκ΄λκ² μ§λ κ² μ΅μ°μ  λͺ©νλ€.<br>
#### 2. νΈμ λ©μλλ₯Ό λλ¬΄ λ§μ΄ λ§λ€μ§ λ§μ.
λͺ¨λ  λ©μλλ κ°κ° μμ μ μμμ λ€ν΄μΌ νλ€. λ©μλκ° λλ¬΄ λ§μ ν΄λμ€λ μ΅νκ³ , μ¬μ©νκ³ , λ¬Έμννκ³ , νμ€νΈνκ³ ,<br>
μ μ§λ³΄μνκΈ° μ΄λ ΅λ€. λ°λΌμ νμ μ΄ μμ§ μμΌλ©΄ λ§λ€μ§ λ§μ.<br>
#### 3. λ§€κ°λ³μ λͺ©λ‘μ μ§§κ² μ μ§νμ.
4κ° μ΄νκ° μ’λ€. μΌλ¨ 4κ°κ° λμ΄κ°λ©΄ λ§€κ°λ³μλ₯Ό μ λΆ κΈ°μ΅νκΈ°κ° μ½μ§μλ€. κ°μ νμμ λ§€κ°λ³μ μ¬λ¬ κ°κ° μ°λ¬μ <br>
λμ€λ κ²½μ°κ° νΉν ν΄λ‘­λ€. κ³Όνκ² κΈ΄ λ§€κ°λ³μ λͺ©λ‘μ μ§§κ² μ€μ¬μ£Όλ κΈ°μ  μΈ κ°μ§λ₯Ό μκ°νκ² λ€.<br>
1) μ¬λ¬ λ©μλλ‘ μͺΌκ° λ€. μͺΌκ°μ§ λ©μλ κ°κ°μ μλ λ§€κ°λ³μ λͺ©λ‘μ λΆλΆμ§ν©μ λ°λλ€.<br>
2) λ§€κ°λ³μ μ¬λ¬ κ°λ₯Ό λ¬Άμ΄μ£Όλ λμ°λ―Έ ν΄λμ€λ₯Ό λ§λλ κ²μ΄λ€. (DTO)<br>
3) μμμ λ κΈ°λ²μ νΌν©ν κ²μΌλ‘, κ°μ²΄ μμ±μ μ¬μ©ν λΉλ ν¨ν΄μ λ©μλ νΈμΆμ μμ©νλ€κ³  λ³΄λ©΄ λλ€.<br>

<br>

## π―  μμ΄ν 52. λ€μ€μ μλ μ μ€ν μ¬μ©νλΌ.
* μ»¬λ μ λΆλ₯κΈ° - μ€λ₯! μ΄ νλ‘κ·Έλ¨μ λ¬΄μμ μΆλ ₯ν κΉ?<br>
```java
public class CollectionClassifier {
    public static String classify(Set<?> s) {
        return "μ§ν©";
    }

    public static String classify(List<?> lst) {
        return "λ¦¬μ€νΈ";
    }

    public static String classify(Collection<?> c) {
        return "κ·Έ μΈ";
    }

    public static void main(String[] args) {
        Collection<?>[] collections = {
                new HashSet<String>(),
                new ArrayList<BigInteger>(),
                new HashMap<String, String>().values()
        };

        for (Collection<?> c : collections)
            System.out.println(classify(c));
    }
}
```
"μ§ν©","λ¦¬μ€νΈ","κ·Έ μΈ"λ₯Ό μ°¨λ‘λ‘ μΆλ ₯ν  κ² κ°μ§λ§, μ€μ λ‘ μνν΄λ³΄λ©΄ "κ·Έ μΈ"λ§ μΈ λ² μ°λ¬μ μΆλ ₯νλ€.<br>
κ·Έ μ΄μ λ λ€μ€μ μ(overloading, μ€λ²λ‘λ©)λ μΈ classify μ€ μ΄λ λ©μλλ₯Ό νΈμΆν μ§κ° μ»΄νμΌνμμ<br>
μ ν΄μ§κΈ° λλ¬Έμ΄λ€. λ°λΌμ, μ»΄νμΌνμμ λ§€κ°λ³μ νμμ κΈ°μ€μΌλ‘ ν­μ μΈ λ²μ§Έ λ©μλλ§ νΈμΆνλ κ²μ΄λ€.<br>
<br>

μ΄λ¬ν λ¬Έμ λ CollectionClassifierμ λͺ¨λ  classifyλ©μλλ₯Ό νλλ‘ ν©μΉ ν instanceofλ‘ λͺμμ μΌλ‘ κ²μ¬νλ©΄ λ§λν ν΄κ²°λλ€.<br>
```java
public class FixedCollectionClassifier {
    public static String classify(Collection<?> c) {
        return c instanceof Set ? "μ§ν©" :
                c instanceof List ? "λ¦¬μ€νΈ" : "κ·Έ μΈ";
    }

    public static void main(String[] args) {
        Collection<?>[] collections = {
                new HashSet<String>(),
                new ArrayList<BigInteger>(),
                new HashMap<String, String>().values()
        };

        for (Collection<?> c : collections)
            System.out.println(classify(c));
    }
}
```
<br>

νλ‘κ·Έλ§€μ΄ μΈμ΄κ° λ€μ€μ μλ₯Ό νμ©νλ€κ³  ν΄μ λ€μ€μ μλ₯Ό κΌ­ νμ©νλ λ»μ μλλ€. μΌλ°μ μΌλ‘ λ§€κ°λ³μ μκ° κ°μ λλ <br>
λ€μ€μ μλ₯Ό νΌνλ κ² μ’λ€. μν©μ λ°λΌ, νΉν μμ±μλΌλ©΄ μ΄ μ‘°μΈμ λ°λ₯΄κΈ°κ° λΆκ°λ₯ν  μ μλ€. κ·Έλ΄ λλ ν·κ°λ¦΄ λ§ν<br>
λ§€κ°λ³μλ νλ³ννμ¬ μ νν λ€μ€μ μ λ©μλκ° μ νλλλ‘ ν΄μΌ νλ€. <br>

## π―  μμ΄ν 53. κ°λ³μΈμλ μ μ€ν μ¬μ©νλΌ.
μΈμ κ°μκ° μΌμ νμ§ μμ λ©μλλ₯Ό μ μν΄μΌ νλ€λ©΄ κ°λ³μΈμκ° λ°λμ νμνλ€. λ©μλλ₯Ό μ μν  λ νμ λ§€κ°λ³μλ<br>
κ°λ³μΈμ μμ λκ³ , κ°λ³μΈμλ₯Ό μ¬μ©ν  λλ μ±λ₯ λ¬Έμ κΉμ§ κ³ λ €νμ.<br>
* μΈμκ° 1κ° μ΄μμ΄μ΄μΌ νλ κ°λ³μΈμ λ©μλ - μλͺ» κ΅¬νν μ!<br>
```java
static int min(int... args) {
    if (args.length == 0)
        throw new IllegalArgumentException("μΈμκ° 1κ° μ΄μ νμν©λλ€.");
    int min = args[0];
    for (int i = 1; i < args.length; i++)
        if (args[i] < min)
            min = args[i];
    return min;
}
```
μ΄ λ°©μμλ λ¬Έμ κ° λͺ κ° μλ€. κ°μ₯ μ¬κ°ν λ¬Έμ λ μΈμλ₯Ό 0κ°λ§ λ£μ΄ νΈμΆνλ©΄ λ°νμμ μ€ν¨νλ€λ μ μ΄λ€. μ½λλ μ§μ λΆνλ€.<br>
<br>
* μΈμκ° 1κ° μ΄μμ΄μ΄μΌ ν  λ κ°λ³μΈμλ₯Ό μ λλ‘ μ¬μ©νλ λ°©λ²<br>
```java
static int min(int firstArg, int... remainingArgs) {
    int min = firstArg;
    for (int arg : remainingArgs)
        if (arg < min)
            min = arg;
    return min;
}
```
μ²« λ²μ§Έλ‘λ νλ²ν λ§€κ°λ³μλ₯Ό λ°κ³ , κ°λ³μΈμλ λ λ²μ¬λ‘ λ°μΌλ©΄ μμμ λ¬Έμ κ° λ§λν μ¬λΌμ§λ€.<br>
<img width="40%" src="https://user-images.githubusercontent.com/55771326/170811106-1a15c5b5-dbbd-41d9-aa1b-5857a2bb5aad.png">
<br>

## π―  μμ΄ν 54. nullμ΄ μλ, λΉ μ»¬λ μμ΄λ λ°°μ΄μ λ°ννλΌ.
#### nullμ λ°ννλ APIλ μ¬μ©νκΈ° μ΄λ ΅κ³  μ€λ₯ μ²λ¦¬ μ½λλ λμ΄λλ€. κ·Έλ λ€κ³  μ±λ₯μ΄ μ’μ κ²λ μλλ€.
μ»¬λ μμ΄λ λ°°μ΄ κ°μ μ»¨νμ΄λκ° λΉμμ λ nullμ λ°ννλ λ©μλλ₯Ό μ¬μ©ν  λλ©΄ ν­μ λ°©μ΄ μ½λλ₯Ό λ£μ΄μ€μΌ νλ€.<br>
ν΄λΌμ΄μΈνΈμμ λ°©μ΄ μ½λλ₯Ό λΉΌλ¨ΉμΌλ©΄ μ€λ₯κ° λ°μν  μ μλ€. μ€μ λ‘ κ°μ²΄κ° 0κ°μΌ κ°λ₯μ±μ΄ κ±°μ μλ μν©μμλ <br>
μλ λ€μμΌ μ€λ₯κ° λ°μνκΈ°λ νλ€.<br>
* λΉ μ»¬λ μμ λ°ννλ μ¬λ°λ₯Έ μ
```java
public List<Cheese> getCheeses(){
    return new ArrayList<>(cheesesInStock);    
}
```
λλΆλΆμ μν©μμλ μ΄λ κ² νλ©΄ λλ€. κ°λ₯μ±μ μμ§λ§, μ¬μ© ν¨ν΄μ λ°λΌ λΉ μ»¬λ μ ν λΉμ΄ μ±λ₯μ λμ λκ² λ¨μ΄ λ¨λ¦΄ μλ μλ€.<br>
λ€νν ν΄λ²μ κ°λ¨νλ€. λ§€λ² λκ°μ λΉ 'λΆλ³'μ»¬λ μμ λ°ννλ κ²μ΄λ€. ex) `Collections.emptyList`, `Collections.emptySet`, `Collections.emptyMap`<br>

<br>

## π―  μμ΄ν 55. μ΅μλ λ°νμ μ μ€ν νλΌ.
κ°μ λ°ννμ§ λͺ»ν  κ°λ₯μ±μ΄ μκ³ , νΈμΆν  λλ§λ€ λ°νκ°μ΄ μμ κ°λ₯μ±μ μΌλμ λ¬μΌ νλ λ©μλλΌλ©΄<br>
μ΅μλμ λ°νν΄μΌ ν  μν©μΌ μ μλ€. νμ§λ§ μ΅μλ λ°νμλ μ±λ₯ μ νκ° λ€λ°λ₯΄λ, μ±λ₯μ λ―Όκ°ν λ©μλλΌλ©΄ <br>
nullμ λ°ννκ±°λ μμΈλ₯Ό λμ§λ νΈμ΄ λμ μ μλ€. κ·Έλ¦¬κ³  μ΅μλμ λ°νκ° μ΄μΈμ μ©λλ‘ μ°λ κ²½μ°λ λ§€μ° λλ―λ€.<br>

<br>

#### μ΅μλμ μμλ₯Ό μ΅λ 1κ° κ°μ§ μ μλ 'λΆλ³'μ»¬λ μμ΄λ€.
λ³΄ν΅μ Tλ₯Ό λ°νν΄μΌ νμ§λ§ νΉμ  μ‘°κ±΄μμλ μλ¬΄κ²λ λ°ννμ§ μμμΌ ν λ T λμ  Optional<T>λ₯Ό λ°ννλλ‘ μ μΈνλ©΄ λλ€.<br>
<br>
* μ»¬λ μμμ μ΅λκ°μ κ΅¬νλ€(μ»¬λ μμ΄ λΉμμΌλ©΄ μμΈλ₯Ό λμ§λ€).<br>
```java
public static <E extends Comparable<E>> E max(Collection<E> c) {
    if (c.isEmpty())
        throw new IllegalArgumentException("λΉ μ»¬λ μ");

    E result = null;
    for (E e : c)
        if (result == null || e.compareTo(result) > 0)
            result = Objects.requireNonNull(e);

    return result;
}
```
μ΄ λ©μλμμ λΉ μ»¬λ μμ κ±΄λ€λ©΄ `IllegalArgumentException`μ λμ§λ€. μ΄μ μλ Optional<E>λ₯Ό λ°ννλ νΈμ΄<br>
λ λ«λ€κ³  μ΄μΌκΈ° νλλ°, κ·Έλ κ² μμ ν λͺ¨μ΅μ λ€μκ³Ό κ°λ€.<br>
<br>
* μ»¬λ μμμ μ΅λκ°μ κ΅¬ν΄ Optional<E>λ‘ λ°ννλ€.
```java
public static <E extends Comparable<E>> Optional<E> max(Collection<E> c) {
    if (c.isEmpty())
        return Optional.empty();

    E result = null;
    for (E e : c)
        if (result == null || e.compareTo(result) > 0)
            result = Objects.requireNonNull(e);

    return Optional.of(result);
}
```

<br>

* μ»¬λ μμμ μ΅λκ°μ κ΅¬ν΄ Optional<E>λ‘ λ°ννλ€. - μ€νΈλ¦Ό λ²μ 
```java
public static <E extends Comparable<E>> Optional<E> max(Collection<E> c) {
    return c.stream().max(Comparator.naturalOrder());
}
```

<br>


## π―  μμ΄ν 56. κ³΅κ°λ API μμμλ ν­μ λ¬Έμν μ£Όμμ μμ±νλΌ.
λ¬Έμν μ£Όμμ APIλ₯Ό λ¬Έμννλ κ°μ₯ νλ₯­νκ³  ν¨κ³Όμ μΈ λ°©λ²μ΄λ€. κ³΅κ° APIλΌλ©΄ λΉ μ§μμ΄ μ€λͺμ λ¬μμΌ νλ€.<br>
νμ€ κ·μ½μ μΌκ΄λκ² μ§ν€μ. λ¬Έμν μ£Όμμ μμμ HTML νκ·Έλ₯Ό μ¬μ©ν  μ μμμ κΈ°μ΅νλΌ. λ¨, HTML λ©νλ¬Έμλ <br>
νΉλ³νκ² μ·¨κΈν΄μΌ νλ€.<br>

<br>

#### javadoc [click here.](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/javadoc.html)
μλ°λμ μμ€μ½λ νμΌμμ λ¬Έμν μ£Όμμ΄λΌλ νΉμν ννλ‘ κΈ°μ λ μ€λͺμ μΆλ € APIλ¬Έμλ‘ λ³νν΄μ€λ€.<br>


<br>

> Joshua Bloch, γEffective Java 3/Eγ, κ°μλ§΅μ μ?κΉ, νλ‘κ·Έλλ°μΈμ¬μ΄νΈ(2018), p297-342.