# 🔥 [ Chapter8 ] 메서드

## 🎯  아이템 49. 매개변수가 유효한지 검사하라.
#### "오류는 가능한 한 빨리 (발생한 곳에서) 잡아야 한다" <br>
오류를 발생한 즉시 잡지 못하면 해당 오류를 감지하기 어려워지고, 감지하더라도 오류의 발생 지점을 찾기 어려워 진다.<br>
<br>

#### 만약, 매개변수 검사를 제대로 하지 못하면 몇 가지 문제가 생길 수 있다.
메서드가 수행되는 중간에 모호한 예외를 던지며 실패할 수 있다. <br>
매개변수 검사에 실패하면 **실패 원자성**을 어기는 결과를 낳을 수 있다.<br>
**_실패 원자성 : 메서드에서 예외가 발생한 후에도 그 객체는 여전히 유효한 상태_**<br>

`public`과 `protected` 메서드는 매개변수 값이 잘못됐을 때 던지는 예외를 문서화해야 한다.<br>
(`@throws` 자바독 태그를 사용하면 된다.)<br>
```java
    /**
     * (현재 값 mod m) 값을 반환한다. 이 메서드는
     * 항상 음이 아닌 BigInteger를 반환한다는 점에서 remainder 메서드와 다르다.
     * 
     * @param m 계수 (양수여야 한다.)
     * @return 현재 값 mod m
     * @throws ArithmeticException m이 0보다 작거나 같으면 발생한다.
     */
    public BigInteger mod(BigInteger m){
        if (m.signum() <= 0){
            throw new ArithmeticException("계수 (m)는 양수여야 합니다. "+m);
        }
        // ,,, 계산 수행
    }
```
여기서 NullPointException(NPE)에 대한 내용은 기술되어 있지 않지만,<br>
BigInteger 클래스 수준에서 기술했기 때문에 이 클래스의 public 메서드 전체에 적용되기에 각 메서드에 기술 할 필요가 없다.<br>
<br>
#### java.util.Objects.requireNonNull<br>
자바 7에 추가된 메서드로 유연하고 사용하기도 편하니, 더 이상 null 검사를 수동으로 하지 않아도 된다.<br>
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

#### public이 아닌 메서드라면 단언문(assert)을 사용해 매개변수 유효성을 검증할 수 있다.
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

메서드나 생성자를 작성할 때면 그 매개변수들에 어떤 제약이 있을지 생각해야 한다.<br>
그 제약들을 문서화하고 메서드 코드 시작 부분에서 명시적으로 검사해야한다.<br>

## 🎯  아이템 50. 적시에 방어적 복사본을 만들라.
#### 클라이언트가 여러분의 불변식을 깨뜨리려 혈안이 되어 있다고 가정하고 방어적으로 프로그래밍해야 한다.
<br>
* 기간을 표현하는 클래스 - 불변식을 지키지 못했다.<br>

```java
public final class Period {
    private final Date start;
    private final Date end;

    /**
     * @param  start 시작 시각
     * @param  end 종료 시각. 시작 시각보다 뒤여야 한다.
     * @throws IllegalArgumentException 시작 시각이 종료 시각보다 늦을 때 발생한다.
     * @throws NullPointerException start나 end가 null이면 발생한다.
     */
    public Period(Date start, Date end) {
        if (start.compareTo(end) > 0)
            throw new IllegalArgumentException(
                    start + "가 " + end + "보다 늦다.");
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
얼핏 이 클래스는 불변처럼 보이고, 시작 시각이 종료 시각보다 늦을 수 없다는 불변식이 무리 없이 지켜질 것 같다. <br>
하지만 Date가 가변이라는 사실을 이용하면 어렵지 않게 그 불변식을 깨뜨릴 수 있다.<br>
<br>
* Period 인스턴스의 내부를 공격해보자.<br>
```java
Date start = new Date();
Date end = new Date();
Period p = new Period(start,end);
end.setYear(78); // p의 내부를 수정했다.
```
**Date는 낡은 API이니 새로운 코드를 작성할 때는 더 이상 사용하면 안된다.**<br>
<br>
외부 공격으로부터 Period 인스턴스의 내부를 보호하려면 **생성자에서 받은 가변 매개변수 각각을 방어적으로 복사해야한다.**<br>
* 수정한 생성자 - 매개변수의 방어적 복사본을 만든다.<br>
```java
public Period(Date start, Date end) {
    this.start = new Date(start.getTime());
    this.end   = new Date(end.getTime());

    if (this.start.compareTo(this.end) > 0)
        throw new IllegalArgumentException(
                this.start + "가 " + this.end + "보다 늦다.");
}
```
매개변수의 유효성을 검사하기 전에 방어적 복사본을 만들고, 이 본사본으로 유효성을 검사한 점을 주목하자.<br>
<br>

생성자를 수정하면 앞서의 공격은 막아낼 수 있지만, Period 인스턴스는 아직도 변경이 가능하다.<br>
접근자 메서드가 내부의 가변 정보를 직접 드러내기 때문이다.<br>
* 수정한 접근자 - 필드의 방어적 복사본을 반환한다.<br>
```java
public Date start() {
    return new Date(start.getTime());
}

public Date end() {
    return new Date(end.getTime());
}
```
<br>
클래스가 클라이언트로부터 받는 혹은 클라이언트로 반환하는 구성요소가 가변이라면<br>
그 요소는 반드시 방어적으로 복사해야한다.<br>

## 🎯  아이템 51. 메서드 시그니처를 신중히 설계하라.
#### 1. 메서드 이름을 신중이 짓자.
항상 표준 명명 규칙을 따라야 한다. 이해할 수 있고, 같은 패키지에 속한 다른 이름들과 일관되게 짓는 게 최우선 목표다.<br>
#### 2. 편의 메서드를 너무 많이 만들지 말자.
모든 메서드는 각각 자신의 소임을 다해야 한다. 메서드가 너무 많은 클래스는 익히고, 사용하고, 문서화하고, 테스트하고,<br>
유지보수하기 어렵다. 따라서 확신이 서지 않으면 만들지 말자.<br>
#### 3. 매개변수 목록은 짧게 유지하자.
4개 이하가 좋다. 일단 4개가 넘어가면 매개변수를 전부 기억하기가 쉽지않다. 같은 타입의 매개변수 여러 개가 연달아 <br>
나오는 경우가 특히 해롭다. 과하게 긴 매개변수 목록을 짧게 줄여주는 기술 세 가지를 소개하겠다.<br>
1) 여러 메서드로 쪼갠다. 쪼개진 메서드 각각은 원래 매개변수 목록의 부분집합을 받는다.<br>
2) 매개변수 여러 개를 묶어주는 도우미 클래스를 만드는 것이다. (DTO)<br>
3) 앞서의 두 기법을 혼합한 것으로, 객체 생성에 사용한 빌더 패턴을 메서드 호출에 응용한다고 보면 된다.<br>

<br>

## 🎯  아이템 52. 다중정의는 신중히 사용하라.
* 컬렉션 분류기 - 오류! 이 프로그램은 무엇을 출력할까?<br>
```java
public class CollectionClassifier {
    public static String classify(Set<?> s) {
        return "집합";
    }

    public static String classify(List<?> lst) {
        return "리스트";
    }

    public static String classify(Collection<?> c) {
        return "그 외";
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
"집합","리스트","그 외"를 차례로 출력할 것 같지만, 실제로 수행해보면 "그 외"만 세 번 연달아 출력한다.<br>
그 이유는 다중정의(overloading, 오버로딩)된 세 classify 중 어느 메서드를 호출할지가 컴파일타입에<br>
정해지기 때문이다. 따라서, 컴파일타입의 매개변수 타입을 기준으로 항상 세 번째 메서드만 호출하는 것이다.<br>
<br>

이러한 문제는 CollectionClassifier의 모든 classify메서드를 하나로 합친 후 instanceof로 명시적으로 검사하면 말끔히 해결된다.<br>
```java
public class FixedCollectionClassifier {
    public static String classify(Collection<?> c) {
        return c instanceof Set ? "집합" :
                c instanceof List ? "리스트" : "그 외";
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

프로그매이 언어가 다중정의를 허용한다고 해서 다중정의를 꼭 활용하란 뜻은 아니다. 일반적으로 매개변수 수가 같은 때는 <br>
다중정의를 피하는 게 좋다. 상황에 따라, 특히 생성자라면 이 조언을 따르기가 불가능할 수 있다. 그럴 때는 헷갈릴 만한<br>
매개변수는 형변환하여 정확한 다중정의 메서드가 선택되도록 해야 한다. <br>

## 🎯  아이템 53. 가변인수는 신중히 사용하라.
인수 개수가 일정하지 않은 메서드를 정의해야 한다면 가변인수가 반드시 필요하다. 메서드를 정의할 때 필수 매개변수는<br>
가변인수 앞에 두고, 가변인수를 사용할 때는 성능 문제까지 고려하자.<br>
* 인수가 1개 이상이어야 하는 가변인수 메서드 - 잘못 구현한 예!<br>
```java
static int min(int... args) {
    if (args.length == 0)
        throw new IllegalArgumentException("인수가 1개 이상 필요합니다.");
    int min = args[0];
    for (int i = 1; i < args.length; i++)
        if (args[i] < min)
            min = args[i];
    return min;
}
```
이 방식에는 문제가 몇 개 있다. 가장 심각한 문제는 인수를 0개만 넣어 호출하면 런타임에 실패한다는 점이다. 코드도 지저분하다.<br>
<br>
* 인수가 1개 이상이어야 할 때 가변인수를 제대로 사용하는 방법<br>
```java
static int min(int firstArg, int... remainingArgs) {
    int min = firstArg;
    for (int arg : remainingArgs)
        if (arg < min)
            min = arg;
    return min;
}
```
첫 번째로는 평범한 매개변수를 받고, 가변인수는 두 번재로 받으면 앞서의 문제가 말끔히 사라진다.<br>
<img width="40%" src="https://user-images.githubusercontent.com/55771326/170811106-1a15c5b5-dbbd-41d9-aa1b-5857a2bb5aad.png">
<br>

## 🎯  아이템 54. null이 아닌, 빈 컬렉션이나 배열을 반환하라.
#### null을 반환하는 API는 사용하기 어렵고 오류 처리 코드도 늘어난다. 그렇다고 성능이 좋은 것도 아니다.
컬렉션이나 배열 같은 컨테이너가 비었을 때 null을 반환하는 메서드를 사용할 때면 항시 방어 코드를 넣어줘야 한다.<br>
클라이언트에서 방어 코드를 빼먹으면 오류가 발생할 수 있다. 실제로 객체가 0개일 가능성이 거의 없는 상황에서는 <br>
수년 뒤에야 오류가 발생하기도 한다.<br>
* 빈 컬렉션을 반환하는 올바른 예
```java
public List<Cheese> getCheeses(){
    return new ArrayList<>(cheesesInStock);    
}
```
대부분의 상황에서는 이렇게 하면 된다. 가능성은 작지만, 사용 패턴에 따라 빈 컬렉션 할당이 성능을 눈에 띄게 떨어 뜨릴 수도 있다.<br>
다행히 해법은 간단하다. 매번 똑같은 빈 '불변'컬렉션을 반환하는 것이다. ex) `Collections.emptyList`, `Collections.emptySet`, `Collections.emptyMap`<br>

<br>

## 🎯  아이템 55. 옵셔널 반환은 신중히 하라.
값을 반환하지 못할 가능성이 있고, 호출할 때마다 반환값이 없을 가능성을 염두에 둬야 하는 메서드라면<br>
옵셔널을 반환해야 할 상황일 수 있다. 하지만 옵셔널 반환에는 성능 저하가 뒤따르니, 성능에 민감한 메서드라면 <br>
null을 반환하거나 예외를 던지는 편이 나을 수 있다. 그리고 옵셔널을 반환값 이외의 용도로 쓰는 경우는 매우 드믈다.<br>

<br>

#### 옵셔널은 원소를 최대 1개 가질 수 있는 '불변'컬렉션이다.
보통은 T를 반환해야 하지만 특정 조건에서는 아무것도 반환하지 않아야 할때 T 대신 Optional<T>를 반환하도록 선언하면 된다.<br>
<br>
* 컬렉션에서 최댓값을 구한다(컬렉션이 비었으면 예외를 던진다).<br>
```java
public static <E extends Comparable<E>> E max(Collection<E> c) {
    if (c.isEmpty())
        throw new IllegalArgumentException("빈 컬렉션");

    E result = null;
    for (E e : c)
        if (result == null || e.compareTo(result) > 0)
            result = Objects.requireNonNull(e);

    return result;
}
```
이 메서드에서 빈 컬렉션을 건네면 `IllegalArgumentException`을 던진다. 이전에도 Optional<E>를 반환하는 편이<br>
더 낫다고 이야기 했는데, 그렇게 수정한 모습은 다음과 같다.<br>
<br>
* 컬렉션에서 최댓값을 구해 Optional<E>로 반환한다.
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

* 컬렉션에서 최댓값을 구해 Optional<E>로 반환한다. - 스트림 버전
```java
public static <E extends Comparable<E>> Optional<E> max(Collection<E> c) {
    return c.stream().max(Comparator.naturalOrder());
}
```

<br>


## 🎯  아이템 56. 공개된 API 요소에는 항상 문서화 주석을 작성하라.
문서화 주석은 API를 문서화하는 가장 훌륭하고 효과적인 방법이다. 공개 API라면 빠짐없이 설명을 달아야 한다.<br>
표준 규약을 일관되게 지키자. 문서화 주석에 임의의 HTML 태그를 사용할 수 있음을 기억하라. 단, HTML 메타문자는 <br>
특변하게 취급해야 한다.<br>

<br>

#### javadoc [click here.](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/javadoc.html)
자바독은 소스코드 파일에서 문서화 주석이라는 특수한 형태로 기술된 설명을 추려 API문서로 변환해준다.<br>


<br>

> Joshua Bloch, 『Effective Java 3/E』, 개앞맵시 옮김, 프로그래밍인사이트(2018), p297-342.