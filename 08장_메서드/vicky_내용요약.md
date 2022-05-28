# 8장. 메서드


## 아이템 49. 매개변수가 유효한지 검사하라
+ 매개변수의 유효성은 실행 전 검사되어야 한다.
+ 단언문을 사용하면 특별한 에러를 나타내지 않지만 vm 옵션을 주면 에러를 보여준다.
#### null 여부를 검사하는 메서드
```java
 public Foo(Bar bar) {
     this.bar = Objects.requireNonNull(bar);
     this.bar = Objects.requireNonNull(bar, "bar must not be null");
     this.bar = Objects.requireNonNullElse(bar, "");
 }
```
+ 검사를 하지 않아도 되는 경우는 유효성 검사비용이 지나치게 높거나 실용적이지 않은 경우 또는 계산과정에서 암묵적으로 검사가 수행되는 경우를 말한다.

<br><hr><br>

## 아이템 50. 적시에 방어적 복사본을 만들라
### 자바는 안전하다는 생각을 버리자.
+ 네이티브 메서드를 사용하지 않는다. -> 아이템 66
  + 네이티브 메서드란 C나 C++ 같은 네이티브 프로그래밍 언어로 작성한 메서드를 말한다.
  + 네이티브 언어는 컴파일러 언어 중에서 컴파일러의 결과물이 특정 CPU의 기계어인 경우의 언어. 
### 가변 클래스 Date의 문제점
+ Date 클래스의 대부분 메서드는 사용중단되었으므로 사용하면 안된다. (Calendar 클래스도 동일한 문제점)
+ Java 8부터 제공되는 LocalDateTime, ZonedDateTime과 같은 클래스를 사용하는 것을 권장
```java
public final class Period {
    private final Date start;
    private final Date end;
    
    public Period(Date start, Date end) {
        if (start.compareTo(end) > 0)
            throw new IllegalArgumentException(
                    start + "가 " + end + "보다 늦다.");
        this.start = start;
        this.end   = end;
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
    
    // Date는 가변임으로 p 내부가 수정가능해진다.
    Date start = new Date();
    Date end = new Date();
    Period p = new Period(start, end);
    end.setYear(20);
```
### 방어적 복사
+ 매개변수 변환을 시도하려는 외부 공격을 막기 위해서는 방어적 복사가 필요하다.
+ 생성자를 사용한 방어적 복사
+ 멀티스레딩 환경이라면 원본 객체의 유효성을 검사한 후 검사본을 만드는 찰나의 순간에 다른 스레드가 원본 객체를 수정할 위험이 있기 때문이다.
```java
public Period(Date start, Date end) {
    this.start = new Date(start.getTime());
    this.end = new Date(end.getTime());
    
    if (this.start.compareTo(this.end) > ) 
        throw new IllegalArgumentException(this.start + " after " + this.end);
}
```
```java
public Date start() {
    return new Date(start.getTime());
}
public Date end() {
    return new Date(end.getTime());
}
```
### 결론
+ 메서드든 생성자든 클라이언트가 제공한 객체의 참조를 내부의 자료구조에 보관해야 할 때면 그 객체가 잠재적으로 변경될 수 있는지를 생각해야 한다. 확신할 수 없다면 복사본을 만들어 저장해야 한다.
+ 길이가 1이상인 배열은 무조건 가변임을 잊지말자.
+ 호출하는 쪽에서 내부 요소를 수정하지 않는다는 보장이 있다면 방어적 복사를 생략할 수도 있다.

<br><hr><br>

## 아이템 51. 메서드 시그니처를 신중히 설계하라
#### 1. 메서드 명명규칙 -> 아이템 68
#### 2. 불필요한 편의메서드를 만들지 말자.
#### 3. 매개변수 목록을 짧게 유지하자.
+ 여러 매서드로 나눈다.
+ 매개변수 여러 개를 묶어주는 도우미 클래스를 만들어준다.
+ 빌더 패턴을 메서드 호출에 응용한다.
  + 모든 매개변수를 하나로 추상화한 객체를 정의하고 클라이언트에서 필요한 객체의 setter메서드만 호출해서 값을 설정한다.
#### 4. 매개변수 타입으로는 클래스보다 인터페이스가 낫다. -> 아이템 64
#### 5. boolean보다는 원소 2개짜리 열거타입이 낫다.
+ 메서드 이름상 boolean으로 받는 것이 명백한 경우는 제외.
+ 온도계 사례) Thermometer.newInstance(TemperatureScale.CELSIUIS)
  + 이렇게 하는 일을 명확하게 표현하는 것이 더 낫기 때문

<br><hr><br>

## 아이템 52. 다중정의는 신중히 사용하라
### 다중정의(overloading)를 피하는 방법
#### 1. instanceof를 이용한 인자값 확인
+ 다중정의된 메서드의 호출결과는 런타임시에 정해진다.
```java
public class CollectionClassifier {
    // 사례1. classify를 호출했을 때 모두 '그 외'라는 결과가 나온다.
    public static String classify(Set<?> s) {
        return "집합";
    }
    public static String classify(List<?> lst) {
        return "리스트";
    }
    public static String classify(Collection<?> c) {
        return "그 외";
    }
    
    // 사례2. classified를 호출했을 때 '집합','리스트','그 외'의 결과가 모두 나온다.
    // instanceof를 이용한 인자값 확인
    public static String classified(Collection<?> c) {
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
            System.out.println(classify(c)); // 사례1
            System.out.println(classified(c)); // 사례2
    }
}
```
#### 2. 메서드 이름을 다르게 한다.
+ ObjectInputStream read 메서드 
```java
public void writeBoolean(boolean val) throws IOException {
  bout.writeBoolean(val);
}   
public void writeByte(int val) throws IOException  {
  bout.writeByte(val);
}
public void writeShort(int val)  throws IOException {
  bout.writeShort(val);
}
public void writeChar(int val)  throws IOException {
  bout.writeChar(val);
}
```
#### 3. 매개변수 형변환을 통해 기대한 함수가 호출되도록 한다.
```java
public static void main(String[] args) {
    Set<Integer> set = new TreeSet<>();
    List<Integer> list = new ArrayList<>();

    for (int i = -3; i < 3; i++) {
        set.add(i);
        list.add(i);
    }
    for (int i = 0; i < 3; i++) {
        set.remove(i);
        list.remove(i);        
    }
    System.out.println(set + " " + list);  // [-3, -2, -1] [-2, 0, 2]
}
```
+ set에서는 실제 정수 값을 제거했고, list는 인덱스를 제거했다.
+ ArrayList에서 remove를 다중정의 했기 때문에 set과 list의 결과가 다르게 나타나는 것을 알 수 있다.
+ set과 같은 결과가 출력되기 위해서는 list.remove((Integer) i)로 해서 Object를 매개변수로 갖는 remove가 호출될 수 있게 해야 한다.

> public E remove(int index) <br>
> Removes the element at the specified position in this list. Shifts any subsequent elements to the left (subtracts one 
> from their indices).

> public boolean remove(Object o)
> Removes the first occurrence of the specified element from this list, if it is present. If the list does not contain the > element, it is unchanged. More formally, removes the element with the lowest index i such that Objects.equals(o, get(i)) > (if such an element exists). Returns true if this list contained the specified element (or equivalently, if this list 
> changed as a result of the call).

##### 제네릭과 오토박싱을 더한 결과 List 인터페이스가 취약해졌다.

<br><hr><br>

## 아이템 53. 가변인수는 신중히 사용하라
### 가변인수 메서드
+ 인수가 1개일 경우에는 런타임에러가 발생하게 된다.
+ 아래처럼 int 인수와 가변인수를 받으면 문제가 해결된다.
```java
static int min(int firstArg, int... remainingArgs) {
   if (args.length == 0)
       throw new IllegalArgumentException("인수가 1개 이상 필요합니다.");
   int min = args[0];
   for (int i = 1; i < args.length; i++)
       if (args[i] < min)
           min = args[i];
       return min;
}
```
+ 성능에 민감한 상황에서는 가변인수 사용을 고려하자.
  + 일반적으로 많이 쓰이는 개수까지는 다중정의하고 그 이후에는 가변인수를 사용하기도 한다.
### [EnumSet.of 메소드](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/EnumSet.html#of(E,E,E,E,E))
+ EnumSet 열거 타입 집합 생성 비용을 최소화한다.

<br><hr><br>

## 아이템 54. null이 아닌, 빈 컬렉션이나 배열을 반환하라
+ null을 반환하면 클라이언트는 null을 처리하는 코드를 추가로 작성해야 한다는 문제가 있다.
+ null과 빈 컨테이너 반환시의 성능상의 차이는 신경 쓸 수준이 되지 않는다.
+ 빈 컬렉션과 배열은 굳이 새로 할당하지 않고도 반환할 수 있다.
+ 사용패턴에 따라 빈 컬렉션 할당이 성능을 눈에 띄게 떨어뜨릴 수도 있다. 그럴 경우 매번 똑같은 빈 '불변' 컬렉션을 반환하면 된다.
```java
public List<Cheese> getCheese(){
  return cheeseInStock.isEmpty()? Collections.emptyList() : new ArrayList<>(cheeseInStock);
}
```
+ 길이 0짜리 배열을 미리 선언해두고 매번 그 배열을 반환하면 된다. 길이 0인 배열은 모두 불변이기 때문이다.

<br><hr><br>

## 아이템 55. 옵셔널 반환은 신중히 하라
+

<br><hr><br>

## 아이템 56. 공개된 API 요소에는 항상 문서화 주석을 작성하라
+
