# π₯ [ Chapter5 ] μ λ€λ¦­

## π―  μμ΄ν 26. λ‘ νμμ μ¬μ©νμ§ λ§λΌ
ν΄λμ€μ μΈν°νμ΄μ€ μ μΈμ νμ λ§€κ°λ³μκ° μ°μ΄λ©΄, μ΄λ₯Ό μ λ€λ¦­ ν΄λμ€ νΉμ μ λ€λ¦­ μΈν°νμ΄μ€λΌ νλ€.
* μ λ€λ¦­ ν΄λμ€
```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable { 
    ...
}
```
* μ λ€λ¦­ μΈν°νμ΄μ€
```java
public interface List<E> extends Collection<E> { ... }
```
μ λ€λ¦­ ν΄λμ€μ μ λ€λ¦­ μΈν°νμ΄μ€λ₯Ό ν΅νμ΄ μ λ€λ¦­ νμ(generic type)μ΄λΌ νλ€.<br>
κ°κ°μ μ λ€λ¦­ νμμ μΌλ ¨μ λ§€κ°λ³μν νμ(parameterized type)μ μ μνλ€.<br>
μ λ€λ¦­ νμμ νλ μ μνλ©΄ κ·Έμ λΈλ¦° λ‘ νμ(raw type)λ ν¨κ» μ μλλ€.
λ‘ νμμ΄λ μ λ€λ¦­ νμμμ νμ λ§€κ°λ³μλ₯Ό μ ν μ¬μ©νμ§ μμ λλ₯Ό λ§νλ€.
* λ‘ νμ
```java
public class Item26 {
  public static void main(String[] args) {
    // μ λ€λ¦­ νμ λ¦¬μ€νΈ
    List<String> genericTypeList = new ArrayList<>();
    // λ‘ νμ λ¦¬μ€νΈ
    List rawTypeList = new ArrayList();
  }
}
```

* λ‘ νμμ λ¬Έμ μ 
```java
public class Item26 {
  public static void main(String[] args) {
    // λ‘ νμ λ¦¬μ€νΈ
    List rawTypeList = new ArrayList();

    rawTypeList.add("raw type list");
    rawTypeList.add("quedevel");
    // μ€μλ‘ string λ¦¬μ€νΈμ intλ₯Ό λ£λλ€.
    // μ¬κΈ°μ intλ₯Ό κΊΌλ΄κΈ°μ μ μ€λ₯λ₯Ό μ μ μλ€.
    rawTypeList.add(1);
    rawTypeList.add("innotree");

    // νμ§λ§ μ€ννκ² λλ€λ©΄
    rawTypeList.forEach(e -> {
      String temp = (String) e; // java.lang.ClassCastException !! λ°μ
      System.out.println(temp);
    });

  }
}
```
λ€λ₯Έ νμμ κ°μ λ£μ΄μ€λ μ»΄νμΌμμλ λ°κ²¬λμ§ μλ€κ° λ°νμμμΌ μμμ± μ μλ€. μ΄μμ μΌλ‘λ μ»΄νμΌν  λ λ°κ²¬νλ κ²μ΄ μ’λ€.<br>
κ²°κ³Όμ μΌλ‘, λ‘ νμμ μ°λ©΄ μ λ€λ¦­μ΄ μκ²¨μ£Όλ μμ μ±κ³Ό ννλ ₯μ λͺ¨λ μκ² λλ€.<br>

<br>

κ·ΈλΌμλ λ‘ νμμ μ°μ§ λ§λΌλ κ·μΉμλ μμν μμΈκ° λͺ κ° μλ€.
1. **class λ¦¬ν°λ΄μλ λ‘ νμμ μ¨μΌ νλ€.**<br>
   ex) List.class, String[].class, int.classλ νμ©νκ³  List<String>.classμ List<?>.classλ νμ©νμ§ μλλ€.<br>

2. **instanceof μ°μ°μμ κ΄λ ¨μ΄ μλ€. λ°νμμλ μ λ€λ¦­ νμ μ λ³΄κ° μ§μμ§λ―λ‘ instanceof μ°μ°μλ λΉνμ μ  μμΌλμΉ΄λ νμ μ΄μΈμ λ§€κ°λ³μν νμμλ μ μ©ν  μ μλ€.**<br>
```java
if(o instanceof Set){
    Set<?> s = (Set<?>) o;    
}
```
> oμ νμμ΄ Setμμ νμΈν λ€μ μμΌλμΉ΄λ νμμΈ Set<?>λ‘ νλ³νν΄μΌ νλ€. μ΄λ κ²μ¬ νλ³ν(checked cast)μ΄λ―λ‘ μ»΄νμΌ κ²½κ³ κ° λ¨μ§ μλλ€.

## π―  μμ΄ν 27. λΉκ²μ¬ κ²½κ³ λ₯Ό μ κ±°νλΌ.
* λΉκ²μ¬ κ²½κ³ 
  ![μΊ‘μ²5](https://user-images.githubusercontent.com/55771326/166133559-1297d608-d80b-4543-82ca-15aeac1fc99e.PNG)
* κ²°κ³Ό : <br>
  ![μΊ‘μ²4](https://user-images.githubusercontent.com/55771326/166133532-40bd2533-70f6-4ddc-b828-77ac15e34564.PNG)
  μ μ²λΌ μμ­μ΄ νμ΄λΌμ΄νΈλ‘ κ²½κ³ λ₯Ό λ³΄μ¬μ£Όλλ° μ΄λ₯Ό λΉκ²μ¬ κ²½κ³ λΌκ³  νλ€. <br>
  μ΄λ¬ν κ²½κ³ λ€μ ν  μ μλ ν λͺ¨λ μ κ±°νλλ‘ νμ.
* μ»΄νμΌλ¬κ° μλ €μ€ λλ‘ μμ  ( λ€μ΄μλͺ¬λ μ°μ°μλ§μΌλ‘ ν΄κ²° )
  ![μΊ‘μ²6](https://user-images.githubusercontent.com/55771326/166133608-8d1559ae-8762-4fc0-83a2-16adabe05e9d.PNG)

<br>

but. κ²½κ³ λ₯Ό μ κ±°ν  μλ μμ§λ§ νμ μμ νλ€κ³  νμ ν  μ μλ€λ©΄ `@SuppressWarnings("unchecked")` μ λνμ΄μμ λ¬μ κ²½κ³ λ₯Ό μ¨κΈ°μ.
```java
@Target({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE, MODULE})
@Retention(RetentionPolicy.SOURCE)
public @interface SuppressWarnings {
    ...
}
```
β­ `@SuppressWarnings` μ λνμ΄μμ ν­μ κ°λ₯ν ν μ’μ λ²μμ μ μ©νμ.

* `@SuppressWarnings` μ¬μ©ν μμ€
```java
public class Item27 {
    public static void main(String[] args) {
        // λ¬΄μν΄λ μμ ν μ΄μ λ₯Ό μ£ΌμμΌλ‘ λ¨κ²¨λΌ!
        @SuppressWarnings("unchecked") Set<Member> set = new HashSet();
    }
}
```
β­ `@SuppressWarnings` μ λνμ΄μμ μ¬μ©ν  λλ©΄ κ·Έ κ²½κ³ λ₯Ό λ¬΄μν΄λ μμ ν μ΄μ λ₯Ό ν­μ μ£ΌμμΌλ‘ λ¨κ²¨μΌ νλ€.

## π―  μμ΄ν 28. λ°°μ΄λ³΄λ€λ λ¦¬μ€νΈλ₯Ό μ¬μ©νλΌ.

λ°°μ΄κ³Ό μ λ€λ¦­ νμμλ μ€μν μ°¨μ΄κ° λ κ°μ§ μλ€.

<br>

#### 1οΈβ£ λ°°μ΄μ κ³΅λ³μ΄μ§λ§, μ λ€λ¦­μ λΆκ³΅λ³μ΄λ€.<br>
`Sub`κ° `Super`μ νμ νμμ΄λΌλ©΄ λ°°μ΄ `Sub[]`λ λ°°μ΄ `Super[]`μ νμ νμμ΄ λλ€. <br>
λ°λ©΄, μλ‘ λ€λ₯Έ νμ `Type1`, `Type2`κ° μμ λ, `List<Type1>`μ `List<Type2>`μ νμ νμλ μλκ³  μμ νμλ μλλ€. <br>

![μΊ‘μ²7](https://user-images.githubusercontent.com/55771326/166134253-7691cc3a-6272-4869-add6-6d0a508535e9.PNG)

μ΄λ μͺ½μ΄λ  `Long`μ© μ μ₯μμ `String`μ λ£μ μλ μλ€. λ€λ§ λ°°μ΄μμλ κ·Έ μ€μλ₯Ό λ°νμμμΌ μκ² λμ§λ§, λ¦¬μ€νΈλ₯Ό μ¬μ©νλ©΄ μ»΄νμΌν  λ λ°λ‘ μ μ μλ€.

<br>

#### 2οΈβ£ λ°°μ΄μ μ€μ²΄ν(reify)λλ€. <br>
λ°°μ΄μ λ°νμμλ μμ μ΄ λ΄κΈ°λ‘ ν μμμ νμμ μΈμ§νκ³  νμΈνλ€. κ·Έλμ μ μ½λμ λ³΄λ― `Long`λ°°μ΄μ `String`μ λ£μΌλ € νλ©΄ `ArrayStoreException`μ΄ λ°μνλ€. <br>
λ°λ©΄, μ λ€λ¦­μ νμ μ λ³΄κ° λ°νμμλ μκ±°(erasure)λλ€.

* Item28.java
```java
public class Item28 {
    public static void main(String[] args) {
        List<String> strings = new ArrayList<>();
        strings.add("que");
        strings.add("devel");
    }
}
```
* Item28.javaλ₯Ό μ»΄νμΌν Item28.class
```java
public class Item28 {
    public Item28() {
    }

    public static void main(String[] var0) {
        ArrayList var1 = new ArrayList();
        var1.add("que");
        var1.add("devel");
    }
}
```
μ΄λ¬ν μκ±°λ μ λ€λ¦­μ΄ μ§μλκΈ° μ μ λ κ±°μ μ½λμ μ λ€λ¦­ νμμ ν¨κ» μ¬μ©ν  μ μκ² ν΄μ£Όλ λ§€μ»€λμ¦μΌλ‘, μλ°5κ° μ λ€λ¦­μΌλ‘ μμ‘°λ‘­κ² μ νλ  μ μλλ‘ ν΄μ€¬λ€.

<br>

#### λ°°μ΄μ μ λ€λ¦­ νμ, λ§€κ°λ³μν νμ, νμ λ§€κ°λ³μλ‘ μ¬μ©ν  μ μλ€.
μ¦, `new List<E>`, `new List<String>[]`, `new E[]` μμΌλ‘ μμ±νλ©΄ μ»΄νμΌν  λ μ λ€λ¦­ λ°°μ΄ μμ± μ€λ₯λ₯Ό μΌμΌν¨λ€.<br>
κ·Έ μ΄μ λ νμ μμ νμ§ μκΈ° λλ¬Έμ΄λ€. μ΄λ₯Ό νμ©νλ€λ©΄ μ»΄νμΌλ¬κ° μλ μμ±ν νλ³ν μ½λμμ λ°νμμ `ClassCastException`μ΄ λ°μν  μ μλ€.

<br>

* μ λ€λ¦­ λ°°μ΄ μμ±μ νμ©νμ§ μλ μ΄μ  - μ»΄νμΌλμ§ μλλ€.
```java
List<String>[] stringLists = new List<String>[1];   // (1)
List<Integer> intList = List.of(42);                // (2)
Object[] objects = stringLists;                     // (3)
objects[0] = intList;                               // (4)
String s = stringLists[0].get(0);                   // (5)
```
π© **if. μ λ€λ¦­ λ°°μ΄μ μμ±νλ (1)μ΄ νμ©λλ€λ©΄?** <br>
1οΈβ£ (2)λ μμκ° νλμΈ `List<Integer>`λ₯Ό μμ±νλ€.<br>
2οΈβ£ (3)μ (1)μμ μμ±ν `List<String>`μ λ°°μ΄μ `Object` λ°°μ΄μ ν λΉνλ€. λ°°μ΄μ κ³΅λ³μ΄λ μλ¬΄ λ¬Έμ μλ€. <br>
3οΈβ£ (4)λ (2)μμ μμ±ν `List<Integer>`μ μΈμ€ν΄μ€λ₯Ό `Object` λ°°μ΄μ μ²« μμλ‘ μ μ₯νλ€. μ λ€λ¦­μ μκ±° λ°©μμΌλ‘ κ΅¬νλμ΄μ μ΄ μ­μ μ±κ³΅νλ€. <br>
( μ¦, λ°νμμλ `List<Integer>` μΈμ€ν΄μ€μ νμμ λ¨μν `List`κ° λκ³ , `List<Integer>[]` μΈμ€ν΄μ€μ νμμ `List[]`κ° λλ€. λ°λΌμ (4)μμλ `ArrayStoreException`μ μΌμΌν€μ§ μλλ€.) <br>
4οΈβ£ `List<String>` μΈμ€ν΄μ€λ§ λ΄κ² λ€κ³  μ μΈν `stringLists`λ°°μ΄μλ μ§κΈ `List<Integer>` μΈμ€ν΄μ€κ° μ μ₯λΌ μλ€. κ·Έλ¦¬κ³  (5)λ μ΄ λ°°μ΄μ μ²μ λ¦¬μ€νΈμμ μ²« μμλ₯Ό κΊΌλ΄λ € νλ€. <br>
5οΈβ£ μ»΄νμΌλ¬λ κΊΌλΈ μμλ₯Ό μλμΌλ‘ `String`μΌλ‘ νλ³ννλλ°, μ΄ μμλ `Integer`μ΄λ―λ‘ λ°νμμ `ClassCastException`μ΄ λ°μνλ€.<br>

**λ°λΌμ, μ΄λ° μΌμ λ°©μ§νλ €λ©΄ (1)μμ μ»΄νμΌ μ€λ₯λ₯Ό λ΄μΌ νλ€.** <br>
μκ±° λ§€μ»€λμ¦ λλ¬Έμ λ§€κ°λ³μν νμ κ°μ΄λ° μ€μ²΄νλ  μ μλ νμμ `List<?>`μ `Map<?,?>` κ°μ λΉνμ μ  μμΌλ μΉ΄λ νμλΏμ΄λ€. <br>
but, λ°°μ΄μ λΉνμ μ  μμΌλμΉ΄λ νμμΌλ‘ λ§λ€ μλ μμ§λ§, μ μ©νκ² μ°μΌ μΌμ κ±°μ μλ€.

* μμ±μμμ μ»¬λ μμ λ°λ Chooser ν΄λμ€
```java
public class Chooser {
    
    private final Object[] choiceArray;

    public Chooser(Collection choiceArray) {
        this.choiceArray = choiceArray.toArray();
    }
    
    public Object choose(){
        Random rnd = ThreadLocalRandom.current();
        return choiceArray[rnd.nextInt(choiceArray.length)];
    }
}
```
* λ¦¬μ€νΈ κΈ°λ° Chooser - νμ μμ μ± νλ³΄!
```java
public class Chooser<T> {

    private final List<T> choiceArray;

    public Chooser(Collection<T> choiceArray) {
        this.choiceArray = new ArrayList<>(choiceArray);
    }

    public T choose(){
        Random rnd = ThreadLocalRandom.current();
        return choiceArray.get(rnd.nextInt(choiceArray.size()));
    }
}
```


## π―  μμ΄ν 29. μ΄μμ΄λ©΄ μ λ€λ¦­ νμμΌλ‘ λ§λ€λΌ.
> μλ‘μ΄ νμμ μ€κ³ν  λλ νλ³ν μμ΄λ μ¬μ©ν  μ μλλ‘νλΌ. κ·Έλ κ² νλ €λ©΄ μ λ€λ¦­ νμμΌλ‘ λ§λ€μ΄μΌ ν  κ²½μ°κ° λ§λ€.<br>
> κΈ°μ‘΄ νμ μ€ μ λ€λ¦­μ΄μμ΄μΌ νλ κ² μλ€λ©΄ μ λ€λ¦­ νμμΌλ‘ λ³κ²½νμ. κΈ°μ‘΄ ν΄λΌμ΄μΈνΈμλ μλ¬΄ μν₯μ μ£Όμ§ μμΌλ©΄μ, <br>
> μλ‘μ΄ μ¬μ©μλ₯Ό ν¨μ¬ νΈνκ² ν΄μ£Όλ κΈΈμ΄λ€.

<br>

* Object κΈ°λ° μ€ν - μ λ€λ¦­μ΄ μ μ€ν κ°λ ₯ νλ³΄!
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
        if (size == 0)
            throw new EmptyStackException();
        Object result = elements[--size];
        elements[size] = null;
        return result;
    }

    public boolean isEmpty() { 
        return size == 0;
    }

    private void ensureCapacity() {
        if (elements.length == size) 
            elements = Arrays.copyOf(elements, 2 * size + 1);
    }
}
```
β­ μ§κΈ μνμμλ ν΄λΌμ΄μΈνΈκ° μ€νμμ κΊΌλΈ κ°μ²΄λ₯Ό νλ³νν΄μΌ νλλ°, μ΄λ `λ°νμ μ€λ₯`κ° λ  μνμ΄ μ΄λ€.

<br>

`μΌλ° ν΄λμ€`λ₯Ό `μ λ€λ¦­ ν΄λμ€`λ‘ λ§λλ μ²« λ¨κ³λ ν΄λμ€ μ μΈμ νμ λ§€κ°λ³μ(**E**)λ₯Ό μΆκ°νλ μΌμ΄λ€.<br>
```java
public Stack() {
    elements = new E[DEFAULT_INITIAL_CAPACITY];
}
```
β­ νμ§λ§ **E**μ κ°μ μ€μ²΄ν λΆκ° νμμΌλ‘λ λ°°μ΄μ λ§λ€ μ μλ€.

<br>

* **ν΄κ²°μ± μ²« λ²μ§Έ** <br>
  μ λ€λ¦­ λ°°μ΄ μμ±μ κΈμ§νλ μ μ½μ λλκ³  μ°ννλ λ°©λ²μΌλ‘ Object λ°°μ΄μ μμ±ν λ€μ μ λ€λ¦­ λ°°μ΄λ‘ νλ³ν νμ!
```java
// λ°°μ΄ elementsλ push(E)λ‘ λμ΄μ¨ E μΈμ€ν΄μ€λ§ λ΄λλ€.
// λ°λΌμ νμ μμ μ±μ λ³΄μ₯νμ§λ§,
// μ΄ λ°°μ΄μ λ°νμ νμμ E[]κ° μλ Object[]λ€!
@SuppressWarnings("unchecked")
public Stack() {
    elements = (E[]) new Object[DEFAULT_INITIAL_CAPACITY];
}
```
β­ `push λ©μλ`λ₯Ό ν΅ν΄ λ°°μ΄μ μ μ₯λλ μμμ νμμ ν­μ **E**λ€. λ°λΌμ μ΄ λΉκ²μ¬ νλ³νμ νμ€ν μμ νκΈ° λλ¬Έμ `@SuppressWarnings`μΌλ‘ κ²½κ³ λ₯Ό μ¨κΈ΄λ€.

<br>

* **ν΄κ²°μ± λ λ²μ§Έ** <br>
  elements νλμ νμμ **E**[]μμ Object[]λ‘ λ°κΎΈλ κ²μ΄λ€. μ΄ λν `push λ©μλ`λ₯Ό ν΅ν΄ **E** νμλ§ νμ©νλ―λ‘ μμ νλ€.
```java
// λΉκ²μ¬ κ²½κ³ λ₯Ό μ μ ν μ¨κΈ΄λ€.
public E pop() {
    if (size == 0)
        throw new EmptyStackException();

    // pushμμ E νμλ§ νμ©νλ―λ‘ μ΄ νλ³νμ μμ νλ€.
    @SuppressWarnings("unchecked") E result = (E) elements[--size];
    elements[size] = null; // λ€ μ΄ μ°Έμ‘° ν΄μ 
    return result;
}
```

<br>

> μ²« λ²μ§Έ λ°©μμλ νλ³νμ λ°°μ΄ μμ± μ λ¨ ν λ²λ§ ν΄μ£Όλ©΄ λμ§λ§, λ λ²μ§Έ λ°©μμμλ λ°°μ΄μμ μμλ₯Ό μ½μ λλ§λ€ ν΄μ€μΌνλ€. <br>
> λ°λΌμ νμμμλ μ²« λ²μ§Έ λ°©μμ λ μ νΈνλ©° μμ£Ό μ¬μ©νλ€. νμ§λ§ (**E**κ° **Object**κ° μλ ν) λ°°μ΄μ λ°νμ νμμ΄ μ»΄νμΌνμκ³Ό <br>
> λ¬λΌ **ν μ€μΌ**μ μΌμΌν¨λ€.

π© **ν μ€μΌ**(heap pollution)μ΄λ? [click here.](https://velog.io/@adduci/Java-%ED%9E%99-%ED%8E%84%EB%A3%A8%EC%85%98-Heap-pollution)

## π―  μμ΄ν 30. μ΄μμ΄λ©΄ μ λ€λ¦­ λ©μλλ‘ λ§λ€λΌ.
> μ λ€λ¦­ νμκ³Ό λ§μ°¬κ°μ§λ‘, ν΄λΌμ΄μΈνΈμμ μλ ₯ λ§€κ°λ³μμ λ°νκ°μ λͺμμ μΌλ‘ νλ³νν΄μΌ νλ λ©μλλ³΄λ€ <br>
> μ λ€λ¦­ λ©μλκ° λ μμ νλ©° μ¬μ©νκΈ°λ μ½λ€.

<br>

* λ‘ νμ μ¬μ© - μμ© λΆκ°!
```java
public static Set union(Set s1, Set s2) {
    Set result = new HashSet<>(s1);
    result.addAll(s2);
    return result;
}
```
μ»΄νμΌμ λμ§λ§ `Raw use of parameterized class 'Set' `, `Unchecked call to 'addAll(Collection<? extends E>)' as a member of raw type 'java.util.Set'`<br>
μ΄λ¬ν λΉκ²μ¬ κ²½κ³ λ₯Ό λ³΄μ¬μ€λ€. μ€μ  IntelliJμμ μμ€λ₯Ό νμΈν΄λ³΄λ©΄ λΈλμ€μ΄ λ²λ² κ°μλκ±Έ λ³Ό μ μλ€. <br>
<br>

<img width="70%" src="https://user-images.githubusercontent.com/55771326/166870516-7acd8784-fa00-4b74-9e36-3c392e9f9394.PNG">

<br>

* λΉκ²μ¬ κ²½κ³ λ₯Ό μ κ±°νκΈ° μν΄ μ λ€λ¦­ λ©μλλ‘ λ¦¬νν λ§

```java
public static <E> Set<E> union(Set<E> s1, Set<E> s2) {
    Set<E> result = new HashSet<>(s1);
    result.addAll(s2);
    return result;
}
```
λ©μλ μ μΈμμμ μΈ μ§ν©μ μμ νμμ νμ λ§€κ°λ³μλ‘ λͺμνκ³ , λ©μλ μμμλ μ΄ νμ λ§€κ°λ³μλ§ μ¬μ©νκ² μμ νλ©΄ λλ€.


## π―  μμ΄ν 31. νμ μ  μμΌλμΉ΄λλ₯Ό μ¬μ©ν΄ API μ μ°μ±μ λμ΄λΌ.

λ§€κ°λ³μν νμμ λΆκ³΅λ³μ΄λ€. μ¦ μλ‘ λ€λ₯Έ νμ Type1κ³Ό Type2κ° μμ λ List<Type1>μ List<Type2>μ νμ νμλ μμ νμλ μλλ€. <br>
μ¦, List<String>μ List<Object>κ° νλ μΌμ μ λλ‘ μννμ§ λͺ»νλ νμ νμμ΄ λ  μ μλ€. <br>
( [λ¦¬μ€μ½ν μΉν μμΉ](https://ko.wikipedia.org/wiki/%EB%A6%AC%EC%8A%A4%EC%BD%94%ED%94%84_%EC%B9%98%ED%99%98_%EC%9B%90%EC%B9%99) μ μ΄κΈλλ€. ) <br>

* μμΌλμΉ΄λ νμμ μ¬μ©νμ§ μμ pushAll λ©μλ - κ²°ν¨μ΄ μλ€!
```java
// Stackμ public API
public void pushAll(Iterable<E> src){
    for (E e : src) {
        push(e);
    }
}
```
μΈλ»λ³΄λ©΄ μ»΄νμΌλ λκ³  μλ²½ν΄ λ³΄μ΄μ§λ§ κ²°ν¨μ΄ μλ€.

```java
public final class Integer extends Number implements Comparable<Integer>{}
```
```java
public static void main(String[] args) {
    Stack<Number> stack = new Stack<>();
    List<Integer> integers = List.of(1,2,3);
    stack.pushAll(integers);
}
```
Integerλ Numberμ νμνμμμλ λΆκ΅¬νκ³  μ μ½λλ μ€λ₯κ° λ°μνλ€.<br>
<img src="https://user-images.githubusercontent.com/55771326/166873684-d306dc9d-77df-4452-87f3-68384a6adbca.PNG">

<br>

μλ°λ μ΄λ° μν©μ λμ²ν  μ μλ νμ μ  μμΌλμΉ΄λ νμμ΄λΌλ νΉλ³ν λ§€κ°λ³μν νμμ μ§μνλ€.

* E μμ°μ λ§€κ°λ³μμ μμΌλμΉ΄λ νμ μ μ©
```java
public void pushAll(Iterable<? extends E> src){
    for (E e : src) {
        push(e);
    }
}
```
E νμμ νμ₯ν λͺ¨λ  νμμ λ°μ μ μκ²λλ€.

<br>

* μμΌλμΉ΄λ νμμ μ¬μ©νμ§ μμ popAll λ©μλ - κ²°ν¨μ΄ μλ€!
```java
public void popAll(Collection<E> dst){
    while (!isEmpty())
        dst.add(pop());
}
```
μ΄ λν λ¬Έμ μμ΄ μλ²½ν κ±°λΌ μκ°νμ§λ§ κ²°ν¨μ΄ μ‘΄μ¬νλ€.

```java
public static void main(String[] args) {
    Stack<Number> stack = new Stack<>();
    List<Integer> integers = List.of(1,2,3);
    stack.pushAll(integers); // μμΌλ μΉ΄λ μ μ©

    Collection<Object> objects = new ArrayList<>();
    stack.popAll(objects);
}
```
<img src="https://user-images.githubusercontent.com/55771326/166874612-0004595e-406c-406e-842c-d0c0957f28f4.png">

μ μ²λΌ Objectλ Numberμ νμνμμ΄ μλλΌλ μ€λ₯κ° λ°μνκ² λλ€.

* E μλΉμ λ§€κ°λ³μμ μμΌλμΉ΄λ νμ μ μ©
```java
public void popAll(Collection<? super E> dst){
    while (!isEmpty())
        dst.add(pop());
}
```
μ μ°μ±μ κ·Ήλννλ €λ©΄ μμμ μμ°μλ μλΉμμ© μλ ₯ λ§€κ°λ³μμ μμΌλμΉ΄λ νμμ μ¬μ©νλΌ. <br>

* _**but. μλ ₯ λ§€κ°λ³μκ° μμ°μμ μλΉμ μ­ν μ λμμ νλ€λ©΄ μμΌλμΉ΄λ νμμ μ¨λ μ’μ κ² μλ€.**_
> νμ€(PECS) : producer-extends, consumer-super <br>

μ¦, λ§€κ°λ³μν νμ Tκ° μμ°μλΌλ©΄ <? extends T>λ₯Ό μ¬μ©νκ³ , μλΉμλΌλ©΄ <? super T>λ₯Ό μ¬μ©νλΌ.

union λ©μλ
```java
public static <E> Set<E> union(Set<E> s1, Set<E> s2)
```
s1, s2κ° λͺ¨λ μμ°μμ΄λ νμ€κ³΅μμ λ°λΌ
```java
public static <E> Set<E> union(Set<? extends E> s1, Set<? extends E> s2)
```
> β λ°ν νμμλ νμ μ  μμΌλμΉ΄λ νμμ μ¬μ©νλ©΄ μ λλ€. μ μ°μ±μ λμ¬μ£ΌκΈ°λμ»€λ ν΄λΌμ΄μΈνΈ μ½λμμλ μμΌλμΉ΄λ νμμ μ¨μΌ νκΈ° λλ¬Έμ΄λ€.

β­ ν΄λμ€ μ¬μ©μκ° μμΌλμΉ΄λ νμμ μ κ²½ μ¨μΌ νλ€λ©΄ κ·Έ APIμ λ¬΄μ¨ λ¬Έμ κ° μμ κ°λ₯μ±μ΄ ν¬λ€.

## π―  μμ΄ν 32. μ λ€λ¦­κ³Ό κ°λ³μΈμλ₯Ό ν¨κ» μΈ λλ μ μ€νλΌ.
κ°λ³μΈμ λ©μλμ μ λ€λ¦­μ μλ° 5 λ ν¨κ» μΆκ°λμμΌλ μλ‘ μ μ΄μ°λ¬μ§λ¦¬λΌ κΈ°λνκ² μ§λ§, μ¬νκ²λ κ·Έλ μ§ μλ€.<br>
κ°λ³μΈμλ λ©μλμ λκΈ°λ μΈμμ κ°μλ₯Ό ν΄λΌμ΄μΈνΈκ° μ‘°μ ν  μ μκ² ν΄μ£Όλλ°, κ΅¬ν λ°©μμ νμ μ΄ μλ€.

* μ λ€λ¦­κ³Ό κ°λ³μΈμλ₯Ό νΌμ©νλ©΄ νμ μμ μ±μ΄ κΉ¨μ§λ€!
```java
static void dangerous(List<String>... stringLists) {
    List<Integer> intList = List.of(42);
    Object[] objects = stringLists;
    objects[0] = intList; // ν μ€μΌ λ°μ
    String s = stringLists[0].get(0); // ClassCastException
}
```
<img width="50%" src="https://user-images.githubusercontent.com/55771326/166879203-acb4d610-ae74-4897-b362-5ab90dff9039.png">

**_μ μ²λΌ νμ μμ μ±μ΄ κΉ¨μ§λ μ λ€λ¦­ κ°λ³μΈμ λ°°μ΄ λ§€κ°λ³μμ κ°μ μ μ₯νλ κ²μ μμ νμ§ μλ€._** <br>

β­ κ·Έλ λ€λ©΄ μ λ€λ¦­ λ°°μ΄μ νλ‘κ·Έλλ¨Έκ° μ§μ  μμ±νλ κ±΄ νμ©νμ§ μμΌλ©΄μ μ λ€λ¦­ κ°λ³μΈμ λ§€κ°λ³μλ₯Ό λ°λ λ©μλλ₯Ό μ μΈν  μ μκ² ν μ΄μ λ λ¬΄μμΌκΉ? <br>

β­ μ μ½λλ₯Ό μ»΄νμΌ μλ¬κ° μλ λΉκ²μ¬ κ²½κ³ λ‘ λλ΄λ μ΄μ κ° λ¬΄μμΌκΉ?

κ·Έ λ΅μ μ λ€λ¦­μ΄λ λ§€κ°λ³μν νμμ κ°λ³μΈμ λ§€κ°λ³μλ₯Ό λ°λ λ©μλκ° μ€λ¬΄μμ λ§€μ° μ μ©νκΈ° λλ¬Έμ΄λ€. <br>
μλ° λΌμ΄λΈλ¬λ¦¬λ μ΄λ° λ©μλλ₯Ό μ¬λΏ μ κ³΅νλλ°, `Arrays.asList(T... a)`, `Collections.addAll(Collection<? superT> c, T... elements)`κ° λνμ μ΄λ€.

```java
@SafeVarargs
public static <T> boolean addAll(Collection<? super T> c, T... elements)
```
```java
@SafeVarargs
public static <T> List<T> asList(T... a)
```

β­ @SafeVarargs μ λνμ΄μμ λ©μλ μμ±μκ° κ·Έ λ©μλκ° νμ μμ ν¨μ λ³΄μ₯νλ μ₯μΉλ€. μ»΄νμΌλ¬λ μ΄ μ½μμ λ―Ώκ³  κ·Έ λ©μλκ° μμ νμ§ μμ μ μλ€λ κ²½κ³ λ₯Ό λ μ΄μ νμ§ μλλ€.<br>
λ°λΌμ, κ°λ³μΈμ λ§€κ°λ³μ λ°°μ΄μ΄ νΈμΆμλ‘λΆν° κ·Έ λ©μλλ‘ μμνκ² μ΄μλ€μ μ λ¬νλ μΌλ§ νλ€λ©΄ κ·Έ λ©μλλ μμ νλ€. <br>

<br>

β οΈ **but, κ°λ³μΈμ λ§€κ°λ³μ λ°°μ΄μ μλ¬΄κ²λ μ μ₯νμ§ μκ³ λ νμ μμ μ±μ κΉ°μλ μμΌλ μ£Όμν΄μΌνλ€.**

* μμ μ μ λ€λ¦­ λ§€κ°λ³μ λ°°μ΄μ μ°Έμ‘°λ₯Ό λΈμΆνλ€. - μμ νμ§ μλ€!
```java
static <T> T[] toArray(T... args) {
    return args;
}

static <T> T[] pickTwo(T a, T b, T c) {
    switch(ThreadLocalRandom.current().nextInt(3)) {
        case 0: return toArray(a, b);
        case 1: return toArray(a, c);
        case 2: return toArray(b, c);
    }
    throw new AssertionError(); // λλ¬ν  μ μλ€.
}

public static void main(String[] args) { // (194μͺ½)
    String[] attributes = pickTwo("μ’μ", "λΉ λ₯Έ", "μ λ ΄ν");
    System.out.println(Arrays.toString(attributes));
}
```
μ μ½λλ μλ¬΄λ° λ¬Έμ κ° μλ λ©μλμ΄λ―λ‘ λ³λ€λ₯Έ κ²½κ³  μμ΄ μ»΄νμΌμ΄ λλ€. νμ§λ§ μ€ννλ € λ€λ©΄ `ClassCastException`μ λμ§λ€. <br>
λ°λ‘ pickTwoμ λ°νκ°μ attributesμ μ μ₯νκΈ° μν΄ String[]λ‘ νλ³νλ μ½λλ₯Ό μ»΄νμΌλ¬κ° μλ μμ±νλ€λ μ μ λμ³€λ€. <br>
Object[]λ String[]μ νμ νμμ΄ μλλ―λ‘ μ΄ νλ³νμ μ€ν¨νλ€. <br>

<img width="50%" src="https://user-images.githubusercontent.com/55771326/166882774-6e43c32b-b754-416d-880d-b78b2c2da420.png">

β­ μμ μλ μ λ€λ¦­μ΄ κ°λ³μΈμ λ§€κ°λ³μ λ°°μ΄μ λ€λ₯Έ λ©μλκ° μ κ·Όνλλ‘ νμ©νλ©΄ μμ νμ§ μλ€λ μ μ λ€μ νλ² μκΈ°μν¨λ€.

## π―  μμ΄ν 33. νμ μμ  μ΄μ’ μ»¨νμ΄λλ₯Ό κ³ λ €νλΌ.

β­ μ΄μ’ μ»¨νμ΄λ ν¨ν΄(type safe heterogeneous container) [click here.](https://datacadamia.com/code/design_pattern/typesafe_heterogeneous_container) <br>

<br>
μ»¬λ μ APIλ‘ λνλλ μΌλ°μ μΈ μ λ€λ¦­ ννμμλ ν μ»¨νμ΄λκ° λ€λ£° μ μλ νμ λ§€κ°λ³μμ μκ° κ³ μ λμ΄ μλ€. νμ§λ§ μ»¨νμ΄λ μμ²΄κ° μλ ν€λ₯Ό νμ λ§€κ°λ³μλ‘ λ°κΎΈλ©΄ <br>
μ΄λ° μ μ½μ΄ μλ νμ μμ  μ΄μ’ μ»¨νμ΄λλ₯Ό λ§λ€ μ μλ€. νμ μμ  μ΄μ’ μ»¨νμ΄λλ Classλ₯Ό ν€λ‘ μ°λ©°, μ΄λ° μμΌλ‘ Class κ°μ²΄λ₯Ό νμ ν ν°μ΄λΌ νλ€. λν, μ§μ  κ΅¬νν <br>
ν€ νμλ μΈ μ μλ€. μμ»¨λ λ°μ΄ν°λ² μ΄μ€μ νμ ννν DatabaseRow νμμλ μ λ€λ¦­ νμμΈ Column<T>λ₯Ό ν€λ‘ μ¬μ©ν  μ μλ€.</T>

```java
public class Favorites {
    // μ½λ 33-3 νμ μμ  μ΄μ’ μ»¨νμ΄λ ν¨ν΄ - κ΅¬ν (200μͺ½)
    private Map<Class<?>, Object> favorites = new HashMap<>();

//    public <T> void putFavorite(Class<T> type, T instance) {
//        favorites.put(Objects.requireNonNull(type), instance);
//    }

    public <T> T getFavorite(Class<T> type) {
        return type.cast(favorites.get(type));
    }

    // μ½λ 33-4 λμ  νλ³νμΌλ‘ λ°νμ νμ μμ μ± νλ³΄ (202μͺ½)
    public <T> void putFavorite(Class<T> type, T instance) {
        favorites.put(Objects.requireNonNull(type), type.cast(instance));
    }

    // μ½λ 33-2 νμ μμ  μ΄μ’ μ»¨νμ΄λ ν¨ν΄ - ν΄λΌμ΄μΈνΈ (199μͺ½)
    public static void main(String[] args) {
        Favorites f = new Favorites();

        f.putFavorite(String.class, "Java");
        f.putFavorite(Integer.class, 0xcafebabe);
        f.putFavorite(Class.class, Favorites.class);

        String favoriteString = f.getFavorite(String.class);
        int favoriteInteger = f.getFavorite(Integer.class);
        Class<?> favoriteClass = f.getFavorite(Class.class);

        System.out.printf("%s %x %s%n", favoriteString,
                favoriteInteger, favoriteClass.getName());
    }
}

```


<br>

> Joshua Bloch, γEffective Java 3/Eγ, κ°μλ§΅μ μ?κΉ, νλ‘κ·Έλλ°μΈμ¬μ΄νΈ(2018), p153-205.