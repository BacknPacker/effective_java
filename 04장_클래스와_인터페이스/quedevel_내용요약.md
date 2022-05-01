# 🔥 [ Chapter4 ] 클래스와 인터페이스

## 🎯  아이템 15. 클래스와 맴버의 접근 권한을 최소화하라.
> 오직 API를 통해서만 다른 컴포넌트와 소통하며 서로의 내부 동작 방식에는 전혀 개의치 않는다.<br>
> 정보 은닉, 혹은 <span style='color:red;'>캡슐화</span>라고 하는 이 개념은 소프트웨어 설계의 근간이 되는 원리이다.

<br>

⭐ **정보 은닉의 핵심 : 모든 클래스와 맴버의 접근성을 가능한 한 좁혀야 한다.**
<img width="60%" src="https://user-images.githubusercontent.com/55771326/164727088-978eab11-1382-4955-95f8-782185c93f5f.png">

<br>


⭐ **`public` 클래스의 인스턴스 필드는 되도록 `public`이 아니어야 한다.**
```java
public class User {
    private long userSn;
    private String userId;
    private String userPw;
}    
```

<br>


⭐ **`public` 가변 필드를 갖는 클래스는 일반적으로 스레드(멀티 스레드에) 안전하지 않다.**

**<span style='color:red;'>스레드 불안정</span> : 상태 제어가 잘못되면 프로그램은 불안정해져서 먹통이 되거나 다운되는것을 말한다.**


<br>


⭐ **클래스에서 `public static final` 배열 필드를 두거나 이 필드를 반환하는 접근자 메서드를 제공해서는 안되다.**
* **이전 코드**
```java
private static final Thing[] PRIVATE_VALUES = { ... };
public static final List<Thing> VALUES = Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUES));
```
* **변경 후 코드**
```java
private static final Thing[] PRIVATE_VALUES = { ... };
public static final Thing[] values(){
        return PRIVATE_VALUES.clone();
        }
```

> `public` 클래스는 상수용 `public static final` 필드 외에는 어떠한 `public` 필드도 가져서는 안된다.<br>
> `public static final` 필드가 참조하는 객체가 <span style='color:red;'>불변</span>인지 확인하라.

## 🎯  아이템 16. public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라.

* **이전 코드**
```java
public class Point {
    // public
    public double x;
    public double y;
}
```
* **변경 후 코드**
```java
public class Point {
    // private
    private double x;
    private double y;

    // getter, setter
    public double getX() { return x; }
    public double getY() { return y; }
    public void setX(double x) { this.x = x; }
    public void setY(double y) { this.y = y; }
}
```
> `public` 클래스는 절대 가변 필드를 직접 노출해서는 안 된다. 불변 필드라면 노출해도<br>
> 덜 위험하지만 완전히 안심할 수는 없다. 하지만 `package-private` 클래스나 `private` <br>
> 중첩 클래스에서는 종종 (불변이든 가변이든) 필드를 노출하는 편이 나을 때도 있다.

## 🎯  아이템 17. 변경 가능성을 최소화하라.
_**불변 클래스는 가변 클래스보다 설계하고 구현하고 사용하기 쉬우며, 오류가 생길 여지도 적고 훨씬 안전하다.**_

* 클래스를 불변으로 만들기 위한 5가지 규칙
> 1️⃣ 객체의 상태를 변경하는 메서드(setter)를 제공하지 않는다. <br>
> 2️⃣ 클래스를 확장할 수 없도록 한다. <br>
> 3️⃣ 모든 필드를 final로 선언한다. <br>
> 4️⃣ 모든 필드를 private으로 선언한다. <br>
> 5️⃣ 자신 외에는 내부의 가변 컴포넌트에 접근할 수 없도록 한다. <br>

* 예제 ) 불변의 복소수 클래스
```java
// class를 final로 선언하여 확장할 수 없도록 함
public final class Complex {
    // 모든 필드를 private final로 선언
    private final double re;
    private final double im;

    public Complex(double re, double im) {
        this.re = re;
        this.im = im;
    }

    // getter만 존재 setter는 만들지 않는다.
    public double getRe() { return re; }
    public double getIm() { return im; }
    
    public Complex plus(Complex complex) { return new Complex(re + complex.re, im + complex.im); }
    
    ...생략
}
```
setter 제공하지 않으며 생성자로 인하여 불변식 설정이 모두 완료된, 초기화가 완벽히 끝난 상태의 객체를 생성하게 된다.<br>

<br>

* 불변 클래스의 장점
> 1️⃣ 불변 객체는 근본적으로 스레드 안전하여 따로 동기화할 필요 없다. <br>
> 2️⃣ 불변 객체는 안심하고 공유할 수 있다. <br>
> 3️⃣ 불변 객체는 자유롭게 공유할 수 있음은 물론, 불변 객체끼리는 내부 데이터를 공유할 수 있다. <br>
> 4️⃣ 불변 객체는 그 자체로 실패 원자성을 제공한다. <br>

## 🎯  아이템 18. 상속보다는 컴포지션을 사용하라.
_**상속은 상위 클래스가 어떻게 구현되느냐에 따라 하위 클래스의 동작에 이상이 생길 수 있다.<br>
그 여파로 코드 한 줄 건드리지 않은 하위 클래스가 오동작할 수 있다.<br>**_

* 잘못된 예 - 상속을 잘못 사용했다.
```java
public class InstrumentedHashSet<E> extends HashSet<E> {
    private int addCount = 0;
    public InstrumentedHashSet(){};
    public InstrumentedHashSet(int initCap, float loadFactor){
        super(initCap, loadFactor);
    }

    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c); // add(e)를 호출한다.
    }

    public int getAddCount(){
        return addCount;
    }

    public static void main(String[] args) {
        InstrumentedHashSet<String> s = new InstrumentedHashSet<>();
        s.addAll(List.of("ㄱ","ㄴ","ㄷ"));
        System.out.println("s.getAddCount() : "+s.getAddCount());
    }
}
```
예상한 결과값은 `3`이겠지만 실제로는 6을 반환한다.
<img width="50%" src="https://user-images.githubusercontent.com/55771326/164878844-8c7f48d3-078f-4582-ac36-04fa740275f7.PNG">

```java
public boolean addAll(Collection<? extends E> c) {
    boolean modified = false;
    for (E e : c)
        if (add(e))
            modified = true;
    return modified;
}
```
`super.addAll(c)`의 구현체에서 `add(e)`를 실행하기 때문이다.

⭐ **이러한 문제를 모두 피해가는 묘안으로 컴포지션을 사용하자.** <br>
_새 클래스의 인스턴스 메서드들은 기존 클래스의 대응하는 메서드를 호출해 그 결과를 반환한다.<br>
이 방식을 `forwarding`이라 하며, 새 클래스의 메서드들을 전달 메서드라 부른다._

* 래퍼 클래스 - 상속 대신 컴포지션을 사용
```java
public class InstrumentedSet<E> extends ForwardingSet<E> {
    private int addCount = 0;

    public InstrumentedSet(Set<E> s) {
        super(s);
    }

    @Override public boolean add(E e) {
        addCount++;
        return super.add(e);
    }
    @Override public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }
    public int getAddCount() {
        return addCount;
    }

    public static void main(String[] args) {
        InstrumentedSet<String> s = new InstrumentedSet<>(new HashSet<>());
        s.addAll(List.of("ㄱ", "ㄴ", "ㄷ"));
        System.out.println("s.getAddCount() = "+s.getAddCount());
    }
}
```
* 재사용할 수 있는 전달 클래스
```java
public class ForwardingSet<E> implements Set<E> {
    private final Set<E> s;
    public ForwardingSet(Set<E> s) { this.s = s; }

    public int size()                 { return s.size();      }
    public boolean add(E e)           { return s.add(e);      }
    public boolean addAll(Collection<? extends E> c)
    { return s.addAll(c);      }
    
    ... 생략
}
```
하나는 집합 클래스 자신이고, 다른 하나는 전달 메서드만으로 이뤄진 재사용 가능한 전달 클래스다.<br>
<img width="50%" src="https://user-images.githubusercontent.com/55771326/164879225-0ace43e3-0dc7-419b-81b0-af6f8e45f9be.PNG">

<br>

> **상속**은 강력하지만 **캡슐화**를 해친다는 문제가 있다. **상속**은 상위 클래스와 하위 클리스가<br>
> 순수한 is-a 관계일 때만 써야한다. is-a 관계일 때도 안심할 수만은 없는 게, 하위 클래스의<br>
> 패키지가 상위 클래스와 다르고, 상위 클래스가 확장을 고려해 설계되지 않았다면<br>
> 여전히 문제가 될 수 있다. **상속**의 취약점을 피하려면 **상속** 대신 **컴포지션**과 전달을 사용하자.<br>
> 특히 래퍼 클래스로 구현할 적당한 인터페이스가 있다면 더욱 그렇다. 래퍼 클래스는<br>
> 하위 클래스보다 견고하고 강력하다.

## 🎯  아이템 19. 상속을 고려해 설계하고 문서화하라. 그러지 않았다면 상속을 금지하라.
> **상속용 클래스를 설계하기란 결코 만만치 않다. 클래스 내부에서 스스로를 어떻게 사용하는지 <br>**
> **모두 문서로 남겨야 하며, 일단 문서화한 것은 그 클래스가 쓰이는 한 반드시 지켜야 한다. <br>**
> **그러지 않으면 그 내부 구현 방식을 믿고 활용하던 하위 클래스를 오동작하게 만들 수 있다. <br>**
> **다른 이가 효율 좋은 하위 클래스를 만들 수 있도록 일부 메서드를 protected로 제공해야 할 수도 있다. <br>**
> **그러니 클래스를 확장해야 할 명확한 이유가 떠오르지 않으면 상속을 금지하는 편이 나을 것이다. <br>**
> **상속을 금지하려면 클래스를 final로 선언하거나 생성자 모두를 외부에서 접근할 수 없도록 만들면 된다.**


## 🎯  아이템 20. 추상 클래스보다는 인터페이스를 우선하라.

⭐ **기존 클래스에도 손쉽게 새로운 인터페이스를 구현해넣을 수 있다.**
```java
public class Marine implements GroundAttackUnit, Comparable<Marine> {
    private String name;
    private int health;

    @Override
    public int compareTo(Marine o) {
        return Integer.compare(health, o.getHealth());
    }
    
    /* 인터페이스 override 생략 */
    ...
    /* getter, setter 생략 */
    ...
}
```
이처럼 자바 플랫폼에서도 Comparable, Iterable, AutoCloseable 인터페이스가 새로 추가됐을 때 <br>
표준 라이브러리의 수많은 기존 클래스가 이 인터페이스들을 구현한 채 릴리스 됐다.

<br>

⭐ **인터페이스는 믹스인(mixin) 정의에 안성맞춤이다.** <br>
믹스인이란 클래스가 구현할 수 있는 타입으로, 믹스인을 구현한 클래스에 원래의 '주된 타입' 외에도 <br>
특정 선택적 행위를 제공한다고 선언하는 효과를 준다. 위 예제에서 `Comparable`은 자신을 구현한 클래스의<br>
인스턴스들끼리 순서를 정할 수 있다고 선언하는 **믹스인 인터페이스**이다.

<br>

⭐ **인터페이스로는 계층구조가 없는 타입 프레임워크를 만들 수 있다.** <br>
```java
public interface GroundAttackUnit extends AbleToWalk, Assailable {
    void run();
}
```
현실에는 계층을 업격히 구분하기 어려운 개념도 있기에 이 정도의 유연성이 항상 필요하지는 않지만, <br>
이렇게 만들어둔 인터페이스가 결정적인 도움을 줄 수도 있다.

> 인터페이스를 사용하지않고 위와같은 구조를 만들기 위해서는 가능한 조합의 경우의 수를 모두 각각 <br>
> 구현하는 상황이 생긴다. 이는 중복된 코드가 늘어나고 유지 보수가 더 어려워지는 결과를 초래할 수 있다. <br>
> 속성이 n개라면 조합의 수는 2^n개나 된다. 흔히 `조합 폭발(combinatorial explosion)`이라 부르는 현상이다.

<br>

⭐ **핵심 정리** <br>
> 일반적으로 다중 구현용 타입으로는 인터페이스가 가장 적합하다. 복잡한 인터페이스라면 구현하는 수고를 덜어주는  <br>
> 골격 구현을 함께 제공하는 방법을 꼭 고려해보자. 골격 구현은 '가능한 한' 인터페이스의 디폴트 메서드로 제공하여  <br>
> 그 인터페이스를 구현한 모든곳에서 활용하도록 하는 것이 좋다. '가능한 한'이라고 한 이유는, 인터페이스에 걸려 있는  <br>
> 구현상의 제약 때문에 골격 구현을 추상 클래스로 제공하는 경우가 더 흔하기 때문이다.

## 🎯  아이템 21. 인터페이스는 구현하는 쪽을 생각해 설계하라.
⭐ **자바 8에 와서 기존 인터페이스에 메서드를 추가할 수 있도록 디폴트 메서드를 소개했지만 그 위험이 완전히 사라진 것은 아니다.** <br>
자바 8에서는 핵심 컬렉션 인터페이스들에 다수의 디폴트 메서드가 추가되었다. 주로 람다를 활용하기 위해서다.<br>
하지만, 생각할 수 있는 모든 상황에서 불변식을 해치지 않는 디폴트 메서드를 작성하기란 어려운 법이다.

* 자바 8의 Collection 인터페이스에 추가된 디폴트 메서드
```java
default boolean removeIf(Predicate<? super E> filter) {
    Objects.requireNonNull(filter);
    boolean removed = false;
    final Iterator<E> each = iterator();
    while (each.hasNext()) {
        if (filter.test(each.next())) {
            each.remove();
            removed = true;
        }
    }
    return removed;
}
```
> 이 코드보다 더 범용적으로 구현하기도 어렵겠지만, 그렇다고 해서 현존하는 모든 Collection 구현체와 잘 어우러지는 것은 아니다.<br>
> 대표적인 예가 아파치의 `SynchronizedCollection`이다. 아파치 버전은 클라이언트가 제공한 객체로 락을 거는 능력을 추가로 제공한다. <br>
> 즉, 모든 메서드에서 주어진 락 객체로 동기화한 후 내부 컬렉션 객체에 기능을 위임하는 래퍼 클래스이다.<br>
> 하지만, `removeIf`의 구현은 동기화에 관한 아무것도 모르므로 락 객체를 사용할 수 없다. 따라서 `SynchronizedCollection` 인스턴스를<br>
> 여러 스레드가 공유하는 환경에서 한 스레드가 `removeIf`를 호출하면 `ConcurrentModificationException`이 발생하거나 다른 예기치<br>
> 못한 결과로 이어질 수 있다.

* 디폴트 메서드는 (컴파일에 성공하더라도) 기존 구현체에 런타임 오류를 일으킬 수 있다.
* 인터페이스를 설계할 때는 여전히 세심한 주의를 기울여야 한다.
* 인터페이스를 릴리스한 후라도 결함을 수정하는 게 가능한 경우도 있겠지만, 절대 그 가능성에 기대서는 안된다.


## 🎯  아이템 22. 인터페이스는 타입을 정의하는 용도로만 사용하라.

⭐ **인터페이스는 자신을 구현한 클래스의 인스턴스를 참조할 수 잇는 타입 역할을 한다.** <br>
달리 말해, 클래스가 어떤 인터페이스를 구현한다는 것은 자신의 인스턴스로 무엇을 할 수 있는지를 클라이언트에 애기해주는 것이다. <br>
**_인터페이스는 오직 이 용도로만 사용해야한다._**

* 상수 인터페이스 안티패턴 - **<span style='color:red;'>사용 금지!</span>**
```java
public interface PhysicalConstants {
    // 아보가드로 수 (1/몰)
    static final double AVOGADROS_NUMBER = 6.022_140_857e23;
    // 볼츠만 상수 (J/K)
    static final double BOLTZMANN_CONSTANT = 1.380_648_52e-23;
    // 전자 질량 (kg)
    static final double ELECTRON_MASS = 9.109_383_56e-31;
}
```
1️⃣ 클래스 내부에서 사용하는 상수는 외부 인터페이스가 아니라 내부 구현에 해당한다.<br>
2️⃣ 상수 인터페이스를 구현하는 것은 이 내부 구현을 클래스의 API로 노출하는 행위이다.<br>
3️⃣ 클래스가 어떤 상수 인터페이스를 사용하든 사용자에게는 아무런 의미가 없다.<br>
4️⃣ 다음 릴리스에서 이 상수들을 더는 쓰지 않게 되더라도 **바이너리 호환성**을 위해 여전히 상수 인터페이스를 구현하고 있어야 한다.<br>


## 🎯  아이템 23. 태그 달린 클래스보다는 클래스 계층구조를 활용하라.
* 태그 달린 클래스 - **<span style='color:red;'>클래스 계층구조보다 훨씬 나쁘다!</span>**
```java
public class Figure {
    enum Shape { RECTANGLE, CIRCLE };

    // 태그 필드 - 현재 모양을 나타낸다.
    final Shape shape;

    // 다음 필드들은 모양이 사각형(RECTANGLE)일 때만 쓰인다.
    double length;
    double width;

    // 다음 필드는 모양이 원(CIRCLE)일 때만 쓰인다.
    double radius;

    // 원용 생성자
    Figure(double radius) {
        shape = Shape.CIRCLE;
        this.radius = radius;
    }

    // 사각형용 생성자
    Figure(double length, double width) {
        shape = Shape.RECTANGLE;
        this.length = length;
        this.width = width;
    }

    double area() {
        switch(shape) {
            case RECTANGLE:
                return length * width;
            case CIRCLE:
                return Math.PI * (radius * radius);
            default:
                throw new AssertionError(shape);
        }
    }
}
```
1️⃣ 태그 달린 클래스는 장황하게, 오류를 내기 쉽고, 비효율적이다.<br>
2️⃣ 태그 달린 클래스는 클래스 계층구조를 어설프게 흉내낸 아류일 뿐이다.<br>

## 🎯  아이템 24. 맴버 클래스는 되도록 static으로 만들라.
* 예제 ) 빌더 패턴
```java
public class Member {
    private long mbrSn;
    private String mbrId;
    private String mbrPw;
    private String mbrNm;

    public Member(Builder builder) {
        this.mbrSn = builder.mbrSn;
        this.mbrId = builder.mbrId;
        this.mbrPw = builder.mbrPw;
        this.mbrNm = builder.mbrNm;
    }

    public static Builder builder(String id, String pw) {
        return new Builder(id, pw);
    }

    public static class Builder {
        public long mbrSn;
        public String mbrId;
        public String mbrPw;
        public String mbrNm;

        private Builder(String mbrId, String mbrPw) {
            this.mbrId = mbrId;
            this.mbrPw = mbrPw;
        }

        public Builder with(Consumer<Builder> consumer) {
            consumer.accept(this);
            return this;
        }

        public Member build() {
            return new Member(this);
        }
    }
    
    /* getter 생략 */
    ...
}
    
```
> 정적 맴버 클래스(`Builder`)는 다른 클래스 안에 선언되고, 바깥 클래스(`Member`)의 <br>
> private 맴버에도 접근할 수 있다는 점만 제외하고는 일반 클래스와 똑같다.<br>
> 정적 맴버 클래스와 비정적 맴버 클래스의 구문상 차이는 단지 static이 붙어 있고 없고 뿐이지만, 의미상 차이는 의외로 꽤 크다. <br>
> 비정적 맴버 클래스의 인스턴스는 바깥 클래스의 인스턴스와 암묵적으로 연결된다. 그래서 비정적 맴버 클래스의 인스턴스 메서드에서 <br>
> 정규화된 this를 사용해 바깥 인스턴스의 메서드를 호출하거나 바깥 인스턴스의 참조를 가져올 수 있다.

⭐ 그렇다면 비정적 맴버 클래스는 어디에 쓰이나? - 비정적 맴버 클래스는 어댑터를 정의할 때 자주 쓰인다.<br>
즉, 어떤 클래스의 인스턴스를 감싸 마치 다른 클래스의 인스턴스처럼 보이게하는 뷰로 사용하는 것이다.


<br>

* 비정적 맴버 클래스의 흔한 쓰임 - 자신의 반복자 구현

```java
public class MySet<E> extends AbstractSet<E> {
    ... // 생략

    @Override
    public Iterator<E> iterator() {
        return new MyIterator();
    }

    private class MyIterator implements Iterator<E> { ... }
}
```
**_맴버 클래스에서 바깥 인스턴스에 접근할 일이 없다면 무조건 static을 붙여서 정적 맴버 클래스로 만들자._** <br>
static을 생략하면 바깥 인스턴스로의 숨은 외부 참조를 갖게 된다. 이 참조를 저장하려면 시간과 공간이 소비된다. <br>
더 심각한 문제는 가비지 컬렉션이 바깥 클래스의 인스턴스를 수거하는 메모리 누수가 생길 수 있다는 점이다.

⭐ **핵심 정리** <br>
> 중첩 클래스에는 네 가지가 있으며, 각각의 쓰임이 다르다. 메서드 밖에서도 사용해야 하거나 메서드 안에 정의하기엔 너무 <br>
> 길다면 맴버 클래스로 만든다. 맴버 클래스의 인스턴스 각각이 바깥 인스턴스를 참조한다면 비정적으로, 그렇지 않으면 정적으로 <br>
> 만들자. 중첩 클래스가 한 메서드 안에서만 쓰이면서 그 인스턴스를 생성하는 지점이 단 한 곳이고 해당 타입으로 쓰기에 <br>
> 적합한 클래스나 인터페이스가 이미 있다면 익명 클래스로 만들고, 그렇지 않으면 지역 클래스로 만들자.


## 🎯  아이템 25. 톱레벨 클래스는 한 파일에 하나만 담으라.
* javac Main.java Dessert.java
```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by FernFlower decompiler)
//

package com.quecode.chapter4;

public class Main {
    public Main() {
    }

    public static void main(String[] var0) {
        System.out.println("potpie");
    }
}
```

* javac Main.java Utensil.java
```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by FernFlower decompiler)
//

package com.quecode.chapter4;

public class Main {
    public Main() {
    }

    public static void main(String[] var0) {
        System.out.println("pancake");
    }
}
```

⭐ **핵심 정리** <br>
> 소스 파일 하나에는 반드시 **_톱레벨 클래스를 하나만 담자._** 이 규칙만 따른다면 컴파일러가 한 클래스에 대한 정의를 여러 개 만들어 내는 <br>
> 일은 사라진다. 소스 파일을 어떤 순서로 컴파일하든 바이너리 파일이나 프로그램의 동작이 달라지는 일은 결코 일어나지 않을 것이다.

## ⭐ 결론
**_추상화의 기본 단위인 클래스와 인터페이스는 자바 언어의 심장과도 같다._** <br>
**_그래서 자바 언어에는 클래스와 인터페이스 설계에 사용하는 강력한 요소가 많이있다._** <br>
**_이번한 요소를 적절히 활용하여 클래스와 인터페이스를 쓰기 편하고, 견고하며, 유연하게 만들자._** <br>