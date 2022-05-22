# 7장. 람다와 스트림

## 아이템 42. 익명 클래스보다는 람다를 사용하라.
### 함수 객체
+ 추상 메서드를 하나만 담은 인터페이스(@FunctionalInterface, java8)의 인스턴스
### 익명 클래스 Anonymous Class
+ 함수 객체를 표현하는 방식으로 사용되는 Inner Class
+ 주로 해당 클래스에서만 사용하고 다른 클래스에서 사용되지 않는 경우
+ 익명 클래스 방식은 코드가 너무 길기 때문에 자바는 함수형 프로그래밍에 적합하지 않았다.
```java
Collection.sort(words, new Comparator<String>() {
  public int compare(String s1, String s2){
    return Integer.compare(s1.length(), s2.length());
  }
});
```
### 람다
+ 함수형 인터페이스의 인스턴스를 람다식으로 더 간결하게 표현할 수 있다.
+ 타입 추론은 컴파일러가 제네릭으로 대신 파악
```java
Collection.sort(words, (s1,s2) -> Integer.compare(s1.length(), s2.length()));
```
##### enum 상수별 추상 메서드 구현
```java
public enum Operation {
    PLUS("+") {
        public double apply(double x, double y) {
            return x + y;
        }
    };

    private final String symbol;

    Operation(String symbol) {
        this.symbol = symbol;
    }

    public abstract double apply(double x, double y);
}
```
```java
public enum OperationLambda {
    PLUS("+", (x, y) -> x + y);

    private final String symbol;
    private final DoubleBinaryOperator op;

    OperationLambda(String symbol, DoubleBinaryOperator op) {
        this.symbol = symbol;
        this.op = op;
    }

    public double apply(double x, double y){
        return op.applyAsDouble(x,y);
    }
}
```
##### 람다 사용시 주의점
+ 람다는 이름이 없으므로 문서화가 어렵다.
+ 열거 타입 생성자에게 넘겨지는 인수들의 타입도 컴파일 타임에 추론된다. 따라서 열거 타입 생성자 안의 람다는 열거타입의 인스턴스 멤버에 접근할 수 없다.
```java
public enum OperationLambda {
    PLUS("+", (x, y) -> System.out.println(symbol);   // ❗️ 열거타입 인스턴스 멤버 접근 불가
                        return x + y;);

    private final String symbol;
    private final DoubleBinaryOperator op;

    OperationLambda(String symbol, DoubleBinaryOperator op) {
        this.symbol = symbol;
        this.op = op;
    }

    public double apply(double x, double y){
        return op.applyAsDouble(x,y);
    }
}
```
### 💡 결론
##### 람다가 대체 할 수 없는것
+ 추상클래스의 인스턴스를 만들때 람다사용은 불가능하다.
+ 추상메서드가 여러 개인 인터페이스의 인스턴스도 람다로 표현 불가능하다.
+ 람다의 this는 바깥 인스턴스를 가리킨다.
  + 함수 객체가 자기 자신을 참조하려는 경우는 익명 클래스를 써야 한다.
+ 직렬화해야만 하는 함수객체가 있다면 private 정적 중첩 클래스의 인스턴스를 사용하자.  

<br><hr><br>

## 아이템 43. 람다보다는 메서드 참조를 사용하라.
### 메서드 참조
+ [생성방법 예시](https://countryxide.tistory.com/127)
+ 람다표현식이 단 하나의 메서드만 호출하는 경우에 람다 표현식에서 불필요한 매개변수를 제거하고 사용할 수 있게 해준다.
+ 메서드를 호출하는 것이 아니라 언제든지 호출할 수 있도록 참조
+ 더 간결하게 표현할 수 있지만 메서드명이 긴 경우에는 람다가 더 적합할 수도 있다.
```java
public class Freq {
    public static void main(String[] args) {
        Map<String, Integer> frequencyTable = new TreeMap<>();
        Map<String, Integer> frequencyTable2 = new TreeMap<>();

        List<String> list = List.of("A","B","C","A");

        for (String s : list)
            frequencyTable.merge(key, 1, (count, incr) -> count + incr); // 람다
            frequencyTable2.merge(key, 1, Integer::sum); // 메서드 참조
            
        System.out.println(frequencyTable);
        System.out.println(frequencyTable2);
    }
}
```
```java
{A=2, B=1, C=1}
{A=2, B=1, C=1}
```
> Map.merge()
>
> default V merge(K key, V value, BiFunction<? super V,? super V,? extends V> remappingFunction)
>
>If the specified key is not already associated with a value or is associated with null, associates it with the given non-null value. Otherwise, replaces the associated value with the results of the given remapping function, or removes if the result is null. This method may be of use when combining multiple mapped values for a key. For example, to either create or append a String msg to a value mapping:
>
> map.merge(key, msg, String::concat)
### 💡 결론
+ 매개변수의 이름 자체가 프로그래머의 좋은 가이드가 되기도 할때에는 메서드 참조보다 람다가 가독성도 좋고 유지보수도 쉽다.
+ 람다로도 못한다면 메서드참조로도 할 수 없다.
+ 제네릭 함수타입을 구현하려고 하는 경우에는 메서드 참조를 사용하자.

<br><hr><br>

## 아이템 44. 표준 함수형 인터페이스를 참조하라
### 자바에서 제공하는 표준 함수형 인터페이스, @FunctionalInterface
##### UnaryOperator
> Returns a unary operator that always returns its input argument.
+ 입력 타입과 리턴 타입이 동일
```java
UnaryOperator<String> uo = (x)->x*x;
System.out.println("UnaryOperator test ="+ uo.apply(5));    // 25
```
##### BinaryOperator
+ 두 인자를 넣었을 때 같은 타입의 객체를 반환
```java
BinaryOperator<Integer> binaryOperator = (n1, n2) -> n1 + n2;
System.out.println(binaryOperator.apply(10, 100));   // 110
``` 
##### Predicate
+ and, isEqual, negate, not, or, test
+ 하나의 인자를 받아 boolean 타입으로 반환
```java
Predicate<Integer> predicate = (num) -> num > 10;
boolean result = predicate.test(100);
System.out.println(result);  // TRUE
```
##### Function
+ Function<T, R> 인자(T)와 반환타입(R)이 다르다.
```java
Function<Integer, Integer> multiply = (value) -> value * 2;
Integer result = multiply.apply(3);
System.out.println(result); // 6
```
##### Supplier
> There is no requirement that a new or distinct result be returned each time the supplier is invoked.
+ 인수를 받지 않고 값을 반환하는 함수
```java
Supplier<String> helloSupplier = () -> "A ";
System.out.println(helloSupplier.get() + "B"   //AB
```
##### Consumer
> Represents an operation that accepts a single input argument and returns no result. Unlike most other functional interfaces, Consumer is expected to operate via side-effects.
+ 인수를 받고, 반환값은 없는 함수
+ accept, andThen
```java
Consumer<String> consumer = s -> System.out.println(s.toUpperCase());
consumer.accept("test");  // TEST
```
### 표준 함수형 인터페이스 사용시 주의점
+ 서로 다른 함수형 인터페이스를 같은 위치의 인수로 받는 메서드들을 다중 정의해서는 안된다.
```java
@Test
void name() {
    ExecutorService executorService = Executors.newSingleThreadExecutor();

    //형변환이 없으면 컴파일 에러를 뱉는다.
    executorService.submit((Runnable) System.out::println);

    //람다는 가능
    executorService.submit(() -> 1);
    executorService.submit(() -> {});
}

https://javabom.tistory.com/86
```

<br><hr><br>

## 아이템 45. 스트림은 주의해서 사용하라.
### 스트림
+ 데이터 원소의 유한 또는 무한 시퀀스
##### 스트림의 출처
```java
// 1. 컬렉션
default Stream<E> stream() {
        return StreamSupport.stream(spliterator(), false);
}

// 2. 배열
public static <T> Stream<T> stream(T[] array) {
            return stream(array, 0, array.length);
}

// 3. 파일
public static Stream<Path> list(Path dir) throws IOException

// 4. 정규표현식 패턴매처 
// Pattern.splitAsStream
public Stream<String> splitAsStream(final CharSequence input) {}

// 5. 난수생성기 
// Random.doubles()
public DoubleStream doubles() {
    return StreamSupport.doubleStream
            (new RandomDoublesSpliterator
                     (this, 0L, Long.MAX_VALUE, Double.MAX_VALUE, 0.0),
             false);
}

// 6. 기본값 
// IntStream, DoubleStream, LongStream
```
### 스트림 파이프라인
+ 원소들을 수행하는 연산 단계
+ 소스스트림 - 중간연산 - 종단연산으로 진행
+ 스트림 파이프라인의 지연평가는 무한스트림을 다룰 수 있게 해주는 열쇠다.
### 스트림은 언제 사용할까?
+ 원소들의 시퀀스를 일관성 있게 변환할 때
+ 원소들의 시퀀스를 필터링할 때
+ 원소들의 시퀀스를 연산 후 결합할 때
+ 원소들의 시퀀스를 모을 때
+ 원소들의 시퀀스 중 특정 조건을 만족하는 원소를 찾을 때
### 스트림 사용시 주의점
+ 과도한 스트림의 사용은 유지보수가 어렵고 성능상 좋지 않다.
+ 자바는 char용 스트림을 지원하지 않는다.
+ 지역변수를 읽고 수정하는 경우는 스트림 사용을 할 수 없다.
+ 람다는  return, break, continue 문이 불가능하므로 스트림 사용을 할 수 없다.

<br><hr><br>

## 아이템 46. 스트림에서는 부작용 없는 함수를 사용하라.
### 스트림 패러다임의 핵심은 계산을 일련의 변환으로 재구성하는 부분이다.
+ 스트림 패러다임의 핵심은 부작용 없는 함수객체
+ 이 때 각 단계는 가능한 한 이전 단계의 결과를 받아 처리하는 순수 함수여야 한다.
+ 순수 함수란 입력만이 결과에 영향을 주는 함수이다.
### 스트림 패러다임을 이해하지 못한 채 API만 사용했다 - 따라 하지 말 것!
```java
Map<String, Long> freq = new HashMap<>();
try (Stream<String> words = new Scanner("file").tokens()) {
words.collect(groupingBy(String::toLowerCase, counting()));
}
```
+ forEach 연산은 스트림 계산 결과를 보고할 때만 사용하고, 계산하는 데는 쓰지 말도록 한다.
### Collectors, 수집기
+ [java.util.stream.Collectors](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/stream/Collectors.html)
+ toList(), toSet(), toCollection(collectionFactory) 등
+ 스트림의 원소들을 하나의 객체에 취합하는 메서드 제공
##### 수집기의 종류
+ toMap, groupingBy, partitioningBy, counting, filtering, mapping, flatMapping, collectingAndThen
+ minBy, maxBy
+ joining
    + CharSequence 인스턴스의 스트림에만 적용할 수 있다.

<br><hr><br>

## 아이템 47. 반환 타입으로는 스트림보다 컬렉션이 낫다
### 스트림은 반복을 지원하지 않는다. 
+ Stream 인터페이스는 Iterable 인터페이스가 정의한 추상 메서드를 전부 포함하며, Iterable 인터페이스가 정의한 방식대로 동작한다.
    + for-each로 반복할 수 없는 이유는 Stream이 Iterable을 extends하지 않았기 때문이다.
### Collection 인터페이스
+ Iterable의 하위타입, stream도 제공
+ 원소 시퀀스를 반환하는 공개 API의 반환 타입에는 Collection이나 그 하위 타입을 쓰는 게 일반적으로 최선이다.
+ 하지만 단지 컬렉션을 반환한다는 이유로 덩치 큰 시퀀스를 메모리에 올려서는 안 된다.
```java
public class PowerSet {
	public static final <E> Collection<Set<E>> of(Set<E> s) {
		List<E> src = new ArrayList<>(s);
		if (src.size() > 30)
			throw new IllegalArgumentException(
				"집합에 원소가 너무 많습니다.(최대 30개)".: + s);
		
		return new AbstractList<Set<E>>() {
			@Override public int size() {
				// 멱집합의 크기는 2를 원래 집합의 원소 수만큼 거듭제곱과 같다. 
				return 1 << src.size();
			}

			@Override public boolean contains(Object o) {
				return o instanceof Set && src.containsAll((Set)o);
			}

			@Override public Set<E> get(int index) {
				Set<E> result = new HashSet<>();
				for (int i = 0; index != 0; i++, index >>=1)
					if ((index & 1) == 1)
						result.add(src.get(i));
					return result;
			}
		};
	}
}
```

<br><hr><br>

## 아이템 48. 스트림 병렬화는 주의해서 적용하라
+ 병렬 스트림 파이프라인 프로그래밍에서도 안전성(Safety)과 응답가능(liveness) 상태를 유지
### 스트림 병렬화
+ 데이터 소스가 Stream.iterate거나 중간 연산으로 limit를 쓰면 파이프라인 병렬화로는 성능 개선을 기대할 수 없다.
##### 병렬화 효과가 좋은 경우
+ 대체로 스트림의 소스가 ArrayList, HashMap, HashSet, ConcurrentHashMap의 인스턴스거나 배열, int 범위, long 범위일 때 병렬화의 효과가 가장 좋다.
+ 나누는 작업은 Spliterator가 담당하며, Spliterator 객체는 Stream이나 Iterable의 spliterator 메서드로 얻어올 수 있다.
+ 참조지역성이 뛰어나다
    + 배열
    + 한번 참조한 데이터는 다시 참조될 가능성이 높고 참조된 데이터 주변의 데이터 역시 같이 참조될 가능성이 높은 성질이다.
    + 실제 객체가 메모리에서 서로 떨어져 있는 경우 참조지역성이 낮다.
##### 종단 연산 중 병렬화에 적합한 것은 축소다.
+ (min, max, count, sum), anyMatch, allMatch, noneMatch처럼 조건에 맞으면 바로 반환되는 메서드도 병렬화에 적합하다. 
+ 반면, 가변 축소를 수행하는 Stream의 collect 메서드는 병렬화에 적합하지 않다.
### 결론
+ 계산도 올바로 수행하고 성능도 빨라질 거라는 확신 없이는 스트림 파이프라인 병렬화는 시도조차 하지 말라. 

<br><hr><br>

## 강의내용
### 아이템 42. 익명 클래스보다는 람다를 사용하라
+ 람다는 intelliJ에서 변환을 안내해준다.
+ 람다를 사용하면, 리팩토링에도 유용한 역할을 한다.
```java
Collections.sort(words, Comparator.comparingInt(s->s.length));
Collections.sort(words, Comparator.comparingInt(String::length));
words.sort(Comparator.comparingIng(String::length));
```
### 아이템 43. 람다보다는 메서드 참조를 사용하라
+ 메서드 참조는 람다의 간단명료한 대안이 될 수 있다. 메서드 참조 쪽이 짥고 명확하면 메서드 참조를 쓰고, 그렇지 않을 때만 람다를 사용하라.
```java
service.excute(GoshThisClassNameIsHumongous::action);
service.excute(()->action);
```
### 아이템 44. 표준 함수형 인터페이스를 사용하라.
+ @FuntionalInterface
+ API를 설계할 때 람다를 고려하라.
+ 43개 인터페이스를 외우지 말고, 6개의 동작방식면 이해하고 있다면, 나머지는 유추가능하다.



