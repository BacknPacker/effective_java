# 5장. 제네릭
<br>

## 아이템 26. 로(Law) 타입은 사용하지 말라.

### 로 타입 대신 제네릭 타입
##### 제네릭 타입이란?
+ 제네릭 클래스, 제네릭 인터페이스
+ 선언에 타입 매개변수가 사용
##### 로 타입이란?
+ 제네릭 타입에서 매개변수를 전혀 사용하지 않은 때
    + 마이그레이션 호환성 문제로 사용
+ 예시, List\<E> 대신 List
##### 로 타입 사용의 단점
> 1. 타입 안전성을 잃는다.
> 2. 에러발생 원인코드가 늦게 발견된다.

### 1. 선언부에서 제네릭을 명시하자
+ (O) 컴파일러에서 인식할 수 있도록 제네릭을 명시한 경우 
```java
private final Collection<Stamp> stamp = ... ;
```  
+ (X) 제네릭을 선언하지 않은 경우, 런타임에서 ClassCastException이 발생하는 사례
```java
private final Collection stamps = ... ;

for (Iterator i = stamps.iterator(); i.hasNext();){
  Stamp stamp = (Stamp) i.next(); // ClassCastException이 발생
  stamp.cancel();
}
```
### 2. List\<String>은 로 타입인 List의 하위 타입이고, List\<Object>의 하위타입이 아니다. (아이템 28)
+ (△) 로 타입 List를 사용하여 List\<String>를 받는 경우, 자동 형변환으로 컴파일 가능.
```java
public static void main(Stringg[] args){
  List<String> strings = new ArrayList<>();
  unsafeAdd(strings, Integer.valueOf(42));
  String s = strings.get(0);
}

public static void unsafeAdd(List list, Object o){
  list.add(0);
}
```
+ (X) List\<Object>로 사용하여 List\<String>를 받는 경우, 컴파일 에러 발생.
```java
public static void main(Stringg[] args){
  List<String> strings = new ArrayList<>();
  unsafeAdd(strings, Integer.valueOf(42));
  String s = strings.get(0);
}

public static void unsafeAdd(List<Object> list, Object o){
  list.add(0);
}
```
### 3. 와일드카드 타입 <?>
+ 로 타입과 달리 null 이외의 어떤 원소도 넣을 수 없다.
+ 대안) 제네릭 메서드나 한정적 와일드카드 타입을 사용하자.
### (예외) 로 타입을 써야 하는 사례
##### 1. class 리터럴 (반드시)
+ List.class, String[].class, int.class
+ [상수와 리터럴](https://jjunii486.tistory.com/43)
  + 변수: 하나의 값을 저장하기 위한 공간
  + 상수: 값을 한 번만 저장하기 위한 공간
  + 리터럴: 그 자체로 값을 의미하는 것
+ [Class Literals](https://aidencom.tistory.com/846)
  + C.class는 Class<C>를 의미한다. Class<C>의 참조값
##### 2. instanceof 연산자 (바람직)
+ (O) instanceof 연산자는 비한정적 와일드카드 타입 이외의 매개변수화 타입에는 적용할 수 없다.
  + 로 타입과 동일하게 작용
```java
if(o instanceof Set){
  Set<?> s = (Set<?> o);
}
```

<br><hr><br>

## 아이템 27. 비검사 경고를 제거하라.
### 할 수 있는 한 모든 비검사 경고를 제거하라.
##### @Suppress Warnings("unchecked") 는 주의해서 사용하자.
+ 지역변수로 한정해서 사용한 사례
+ public static void arraycopy(Object src, int srcPos, Object dest, int destPos, int length)
```java
public<T> T[] toArray(T[] a){
  if(a.length < size){
    @SuppressWarnings("unchecked") T[] result = 
        (T[])Arrays.copyOf(elements, size, a.getClass());
    return result;
  }
  System.arraycopy(elements, 0, a, 0, size);
  if(a.length>size)
    a[size] = null;
  return a;
}
```
##### 안전한 이유를 주석으로 남기자.

<br><hr><br>

## 아이템 28. 배열보다는 리스트를 사용하라.
### 리스트가 권장되는 이유
##### 1. 문제를 컴파일시에 발견할 수 있다.
+ 배열은 공변이지만, 제네릭은 불공변이다.
```java
// 런타임시 문제 발견
Object[] objectArray = new Long[1];
objectArray[0] = "타입이 달라 넣을 수 없다.";

// 컴파일시 문제 발견, 문법오류
List<Object> ol = new ArrayList<Long>();
ol.add("타입이 달라 넣을 수 없다.");
```
##### 2. 배열은 실체화된다.
+ 제네릭은 타입 정보가 런타임에는 소거된다.
+ 제네릭 배열은 생성할 수 없다.
  + 타입 안전성이 보장되지 않는다. 런타임에 ClassCastException 발생
+ 제네릭 배열이 허용 안되는 이유에 대한 구체적인 사례
  + (1)이 가능하다고 가정했을 때, List<String>을 선언했지만 List<Integer> 타입이 담겨있다.
  + 즉, 런타임에 ClassCastException 발생
```java
List<String>[] stringLists = new List<String>[1]; // (1) 
List<Integer> intList = List.of(42);              // (2)
Object[] objects = stringLists;                   // (3)
objects[0] = intList;                             // (4)
String s = stringLists[0].get(0);                 // (5)
```
+ 리스트 기반 제네릭 타입 사용사례
```java
public class Chooser<T> {

  private final List<T> choiceList;

  public Chooser(Collection<T> choices) {
    choiceList = new ArrayList<>(choices);
  }

  public T choose() {
    Random rnd = ThreadLocalRandom.current();
    return choiceList.get(rnd.nextInt(choiceList.size()));
  }
}
```
<br><hr><br>

## 아이템 29. 이왕이면 제네릭 타입으로 만들라
### Object 대신 적절한 타입 매개변수로 바꾸자. (아이템 68)
```java
// (X) E와 같은 실체화 불가 타입으로는 배열을 만들 수 없다.
elements = new E[DEFAULT_INTIAL_CAPACITY];

// (X) 형변환을 해줘도 타입 안전하지 않다.
elements = (E[]) new Object[DEFAULT_INTIAL_CAPACITY];

// (O) 하지만, 위의 형변환 사례가 push(E e) 메서드에서 볼 수 있듯이
// E[] 타입만 elements로 들어오는 것이 확실하다면, 비검사 형변환으로 전환하여 경고를 숨길 수 있다.
// 배열의 런타임 타입이 컴파일 타입과 달라 힙 오염을 일으킬 수 있다.
@SuppressWarnings("unchecked")
public Stack(){
  elements = (E[]) new Object[DEFAULT_INTIAL_CAPACITY];
}

// (O) elements의 필드타입을 Ojbect[] 으로 변경한다.
// 단, 이 경우 배열에서 원소를 읽을 때마다 비검사 형변환을 해줘야 한다.
public Stack(){
  elements = new Object[DEFAULT_INTIAL_CAPACITY];
}
public E pop(){
  if(size==0){
    throw new EmptyStackException();
    
  @SuppressWarnings("unchecked") E result = (E) elements[--size];
  }
}
```
> ❓ 배열의 런타임 타입이 컴파일 타입과 달라 힙 오염을 일으킬 수 있다.
### 제네릭 매개변수 타입
+ 기본타입은 사용할 수 없으나 박싱된 기본타입으로 우회가 가능하다.
##### 한정적 매개변수
```java
class DelayQueue<E extends Delayed> implements BlockingQueue<E>
```

<br><hr><br>

## 아이템 30. 이왕이면 제네릭 메서드로 만들라.
### 제네릭 메서드
+ 아래 union 메서드는 한정적 와일드카드 사용으로 더 유연하게 개선할 수 있다.
```java
public static <E> Set<E> union(Set<E> s1, Set<E> s2) {
  Set<E> result = new HashSet<>(s1);
  result.add(s2);
  return result;  
}
```
> ❓ 원소 순서는 구현방식에 따라 잘라진다.
##### 제네릭 싱글 팩터리 
+ 불변객체를 여러 타입으로 만들고자 하는 경우, 요청한 타입 매개 변수에 맞게 매번 타입을 바꿔주는 정적 팩터리를 만들어야 한다.
##### 항등함수 사례
+ 제네릭 <T>와 <Object>는 다르지만, 항등함수라는 특성상 T 타입을 반환하는 것이 당연시되므로 @SuppressWarnings 처리하자.
##### 재귀적 타입 한정
```java
public interface Comparable<T> {
  int compareTo(T o);
}
// 모든 타입 E는 자신과 비교할 수 있다.
// 빈 Collection이 들어와서 IllegalArgumentException을 던지는 것을 방지하려면,
// return 타입을 Optional<E>로 고치는 것이 바람직하다.
public static <E extends Comparable<E>> E max(Collection<E> c);
```

<br><hr><br>

## 아이템 31. 한정적 와일드카드를 사용해 API 유연성을 높이라.
### 불공변 방식보다 유연성이 필요할 때
+ 유연성을 극대화하려면, 원소의 생산자나 소비자용 입력 매개변수에 와일트카드 타입을 사용하라.
+ 아래 코드에서 Stack<Number>값이 들어가고, pushAll(Iterable<E> src) 메소드에 변수로 Integer 타입이 들어간다면, 
  불공변으로 인한 오류 메시지가 발생한다.
```java
public class Stack<E> {
  public Stack<E>;
  public void push(E e);
}
public void pushAll(Iterable<? extends E> src) {
  for(E e : src) push(e);
}
```
+ 매개변수 타입 T가 생산자라면 <? extends T>를 사용하고, 소비자라면 <? super T>를 사용하라.
+ 반환타입에서는 한정적 와일드카드 타입을 사용하면 안 된다. 
    + 클라이언트 코드에서도 와일드카드 타입을 사용해야 하기 때문.
##### 비한정적 타입 매개변수와 비한정적 와일드카드
+ 메서드 선언에 타입 매개변수가 한 번만 나오면 와일드카드로 대체하라.
```java
public static void swap(List<?> list, int i, int j) {
  swapHelper(list,i,j);
}
// swapHelper라는 메서드를 만들어 와일드카드 타입의 실제 타입을 알려준다.
private static <E> swapHelper(List<E> list, int i, int j) {
  list.set(i, list.set(j, list.get(i)));
}
```
<br><hr><br>

## 아이템 32. 제네릭과 가변인수를 함께 쓸 때는 신중하라
### 가변인수 메서드 (아이템 53)
+ varargs 매개변수에 제네릭이나 매개변수화 타입이 포함되면 알기 어려운 컴파일 경고가 발생한다.
##### 제네릭과 varargs 혼용 → 힙 오염 발생
+ 매개변수화 타입의 변수가 타입이 다른 객체를 참조하면 힙 오염이 발생한다.
```java
static void dangerous(List<String> ... stringLists){
  List<Integer> intList = List.of(42);
  Object[] objects = stringLists;
  objects[0] = intLists;             // 힙  오염 발생
  String s = stringLists[0].get(0);  // ClassCastException
}
```
##### @SafeVarargs 메서드 안전함을 보장하는 장치
+ 메서드가 안전성을 파악하는 방법
  + varargs 매개변수를 담는 제네릭 배열이 만들어지면 아무것도 저장하지 않고, 그 배열의 참조도 밖으로 노출되지 않는다면 타입 안전하다.
+ toArray는 배열 참조를 그대로 노출하고 있어 pickTwo() 메서드 실행시 ClassCastException를 발생시킨다.
  + pickTwo 반환값을 String[]으로 형변환하는 코드를 컴파일러가 자동으로 생성한다.
  + Object[]는 String[]의 하위타입이 아니다.
```java
// (X) 잘못된 사례
static <T> T[] toArray(T... args){
  return args;
}

static <T> T[] pickTwo(T a, T b, T c) {
  switch(ThreadLocalRandom.current().nextInt(3)) {
    case 0: return toArray(a, b);
    case 1: return toArray(a, c);
    case 2: return toArray(b, c);
  }
  throw new AssertionError();
}

// (O) List.of 라이브러리 사용한 사례
static <T> List<T> pickTwo(T a, T b, T c) {
  switch(ThreadLocalRandom.current().nextInt(3)) {
  case 0: return List.of(a, b);
  case 1: return List.of(a, c);
  case 2: return List.of(b, c);
  }
  throw new AssertionError();
}
```

<br><hr><br>

## 아이템 33. 타입 안전 이종 컨테이너를 고려하라.
### 타입 안전 이종 컨테이너 패턴
+ 컨테이너 대신 키를 함께 제공하고 제네릭 타입 시스템이 값의 타입이 키와 같음을 보장
```java
// 타입 안전 이종 컨테이너 구현부
public class Favorites { 
  private Map<Class<?>, Object> favorites = new HashMap<>(); 
  
  public <T> void putFavorite(Class<T> type, T instance) { 
    favorites.put(Objects.requireNull(type), instance); 
  } 
  
  public <T> T getFavorite(Class<T> type) {
    return type.cast(favorites.get(type)); 
  } 
}

// 실행부
public static void main(String[] args) { 
  Favorites f = new Favorites(); 
  
  f.putFavorite(String.class, "Java"); 
  f.putFavorite(Integer.class, 0xcafebabe); 
  f.putFavorite(Class.class, Favorites.class); 
  
  String favoritesString = f.getFavorite(String.class); 
  int favoriteInteger = f.getFavorite(Integer.class); 
  Class<?> favoriteClass = f.getFavorite(Class.class); 
  
  Sytstem.out.printf("%s %x %s%n", favoriteString, favoriteInteger, favoriteClass.getName()); 
}
```
##### 한정적 타입 토큰의 사용
+ 한정적 타입 매개변수나 한정적 와일드카드를 사용해 표현 가능한 타입을 제한하는 타입 토큰
+ Class<?> 타입을 사용하려면, Class의 abSubClass 메서드를 사용하자.
```java
static Annotation getAnnotation(AnnotatedElement element, String annotationTypeName) {
  Class<?> annotationType = null; // 비한정적 타입 토큰 
  try {
    annotationType = Class.forName(annotationType); 
  } catch (Exception ex) {
    throw new IllegalArgumentException(ex); 
  } return element.getAnnotation(
      annotationType.asSubclass(Annotation.class);
  ); 
}
```

<br><hr><br>

### ❗ 강의내용 추가
##### 아이템 26. 로 타입은 사용하지 말라
```java
public interface List<E> extends Collection<E> { }

public class ArrayList<E>
        extends AbstactList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```
##### 아이템 28. 배열 대신 리스트를 사용하라
+ 차라리 LinkedList와 ArrayList의 차이점을 잘 알고 쓰는 것이 더 중요하다.
+ HashMap의 검색속도가 빠른 이유
##### 아이템 29. 이왕이면 제네릭 타입으로 만들라
+ Object를 이용한 직접 형변환하는 코드 대신 제네릭 타입으로 만들어 형변환하는 코드를 없애라.
+ 기존 형변환 코드가 있다면 리팩토링할 것.
