# 9장. 일반적인 프로그래밍 원칙

## 아이템 57. 지역변수의 범위를 최소화하라
+ 클래스와 멤버의 접근 권한을 최소화하라 -> 아이템 15
+ 지역변수를 생각없이 선언하다 보면 변수가 쓰이는 범위보다 너무 앞서 선언하건, 다 쓴 뒤에도 여전히 살아 있게 되기 쉽다.
+ 거의 모든 지역변수는 선언과 동시에 초기화해야 한다.
### while문보다 for문을 이용하자
+ 변수 유효범위가 for문 범위와 유효해서 변수 유효성이 반복문 종료와 함께 끝난다.
+ for문에 사용된 유효하지 않은 변수는 컴파일 에러를 발생
+ 가독성이 좋다.
### 지역변수 범위를 최소화하는 방법
1. 관용구의 활용
```java
// 지역변수 범위를 최소화하는 반복문 관용구
for(int i=0; n=expensiveComputation; i<n; i++){
  ...
}
```
2. 메서드를 작게 유지하고 한 가지 기능에만 집중하는 것

<br><hr><br>

## 아이템 58. 전통적인 for문보다는 for-each문을 사용하라
### for-each문 (향상된 for문)
+ Iterable<E> 구현한 객체라면 무엇이든 순회할 수 있다.
#### for-each문이 가지는 장점
+ 아래의 코드에서는 NoSuchElementException 발생
+ Suit 하나당 
```java
enum Suit { CLUB, DIAMOND, HEART, SPADE }
enum Rank { ACE, DEUCE, THREE, FOUR, FIVE, SIX, SEVEN, EIGHT, NINE, TEN, JACK, QUEEN, KING }

static Collection<Suit> suits = Arrays.asList(Suit.values());
static Collection<Rank> ranks = Arrays.asList(Rank.values());

List<Card> deck = new ArrayList<>();
for (Iterator<Suit> i = suits.iterator() ; i.hasNext() ;) {
    for (Iterator<Rank> j = ranks.iterator() ; j.hasNext() ;){
      deck.add(new Card(i.next(),j.next()));    
    }
}
```
+ 위 문제점은 향상된 for문을 사용하면 해결할 수 있다.
```java
List<Card> deck = new ArrayList<>();
for (Suit suit : suits)
  for (Rank rank : ranks)
    deck.add(new Card(suit, rank));
```
### for-each문을 사용할 수 없는 경우들
1. 파괴적인 필터링
   + 컬렉션을 순회하면서 선택된 원소를 제거해야 한다면 remove 메소드를 호출해야 한다.
2. 변형 
   + 리스트나 배열을 순회하면서 그 원소의 값 일부 혹은 전체를 교체해야 한다면 리스트의 반복자나 배열의 인덱스를 사용해야 한다.
3. 병렬 반복
   + 여러 컬렉션을 병렬로 순회해야 한다면 각각의 반복자와 인덱스 변수를 사용해 엄격하고 명시적으로 제어해야 한다.

<br><hr><br>

## 아이템 59. 라이브러리를 익히고 사용하라
### Random 메서드의 결함
```java
static Random rnd = new Random();
static int random(int n){
  return Math.abs(rnd.nextInt())%n;
}
```
+ n 값이 크지 않은 2의 제곱수라면 같은 수열이 반복된다.
+ 무작위 수가 중간 값보다 작은 수들에 쏠려있다.
+ 지정범위 외의 수가 나올 수 있다.
#### Random 메서드의 보안
+ Random.nextInt(int)
+ ThreadLocalRandom
+ SplittableRandom (포크-조인풀이나 병렬 스트림에서)
### 표준 라이브러리 사용의 이점
1. 전문가들의 지식과 경험이 반영된 메서드
2. 핵심적인 일이 아닌 것에 시간을 소비하지 않아도 된다
3. 노력하지 않아도 성능이 지속해서 개선
4. 기능이 점점 많아진다
5. 다른 개발자가 이해할 수 있는 낯익은 코드가 된다
+ 적어도 java.lang, java.util(java.util.concurrent), java.io와 하위 패키지들에는 익숙해지자!

<br><hr><br>

## 아이템 60. 정확한 답이 필요하다면 float과 double은 피하라
+ 금융 관련 계산에서는 BigDecimal, int, long을 사용해야 한다
+ BigDecimal은 기본타입보다 불편하고 훨씬 느리다.

<br><hr><br>

## 아이템 61. 박싱된 기본타입보다는 기본타입을 사용하라
### 기본타입과 박싱된 기본타입의 차이
1. 박싱된 기본타입은 기본타입이 가지는 값 + 식별성을 가진다.
   ```java     
   Comparator<Integer> naturalOrder = (i, j) -> (i < j) ? -1 : (i == j ? 0 : 1);
   int num = naturalOrder.compare(new Integer(42), new Integer(42));    // 1
   ```
2. 박싱된 기본 타입은 유효하지 않은 값을 가질 수 있다. 즉, null을 가질 수 있다.
   ```java
   Integer i;   
   if(i == 43) {
      System.out.println("믿을 수 없네..!");
   }   
   ```
   + 43과 비교 과정에서 i는 언박싱되서 null 참조를 하게 되는데 기본타입에서는 불가능한 일이기 때문에 NullPointException이 발생한다
3. 기본 타입이 박싱된 기본 타입보다 시간과 메모리 사용면에서 더 효율적이다.
### 박싱된 기본타입을 사용해야 할 때
+ 컬렉션의 원소, 키, 값으로 쓴다.
  + 매개변수화 타입이나 매개변수화 메서드의 타입 매개변수로는 박싱된 기본 타입을 사용해야 한다. ThreadLocal<Integer>
+ 리플렉션을 통해 메서드를 호출할 때도 박싱된 기본 타입을 사용해야한다. -> 아이템 65

<br><hr><br>

## 아이템 62. 다른 타입이 적절하다면 문자열 사용을 피하라.
### 문자열 사용을 피해야 하는 경우
+ 다른 값 타입을 대신하기에 적합하지 않다.
+ 열거 타입을 대신하기에 적합하지 않다.
+ 문자열은 혼합타입을 대신하기 적합하지 않다.
   + 차라리 private 정적 멤버 클래스로 선언해서 사용하자.
```java
String compoundKey = className + "#" + i.next();
```
+ 문자열은 권한을 표현하기에 적합하지 않다.
```java
public class ThreadLocal {
    private ThreadLocal(){}
    public static void set(String key, Object value){}
    public static Object get(String key) { return null; }
}
```
```java
// java.lang.ThreadLocal
public class ThreadLocal<T> {
    public ThreadLocal() {}
    public void set(T value) {}
    public  T get() { return null; }
}
```

<br><hr><br>

## 아이템 63. 문자열 연결은 느리니 주의하라
+ 문자열 연결하는 시간은 n^2에 비례한다.
+ 문자열은 불변이라서 두 문자열을 연결할 경우 양쪽의 내용을 모두 복사해야 한다.
+ StringBuilder append 메서드를 사용하라.

<br><hr><br>

## 아이템 64. 객체는 인터페이스를 사용해 참조하라
+ 적합한 인터페이스가 있다면 매개변수 뿐 아니라 반환값, 변수, 필드를 전부 인터페이스 타입으로 선언하라.
+ 인터페이스 타입을 사용하는 습관을 길러두면 프로그래밍이 훨씬 유연해진다.
#### 적합한 인터페이스가 없다면?
+ 클래스를 참조해야 된다.
+ 클래스의 계층구조 중에 필요한 기능을 만족하는 가장 덜 구체적인 클래스 타입을 사용하자.
  + 예: java.io.OutputStream

<br><hr><br>

## 아이템 65. 리플렉션보다 인터페이스를 사용하라
### 리플렉션의 단점
+ 컴파일 타임 타입 검사가 주는 이점을 하나도 누릴수 없다.
+ 리플렉션을 이용하면 코드가 지저분해지고 장황해진다.
+ 성능이 떨어진다.
#### 리플렉션은 인스턴스 생성에만 사용하고 이렇게 만든 인스턴스는 상위 클래스나 인터페이스를 참조하여 사용하자.

<br><hr><br>

## 아이템 66. 네이티브 매서드는 신중히 작성하라.
### JNI 자바 네이티브 인터페이스
+ 네이티브 언어로 작성한 메서드
#### 주요 쓰임
1. 레지스터리 같은 플랫폼 특화 기능을 사용한다.
   + 자바 버전이 올라가면서 필요성 감소
2. 네이티브 코드로 작성된 기존 라이브러리를 사용한다.
3. 성능 개선을 목적으로 성능에 결정적인 영향을 주는 부분만 네이티브 코드로 작성한다.
#### 단점
+ 네이티브 메서드가 안전하지 않으므로(아이템 50) 네이티브 메서드를 사용하는 애플리케이션도 메모리 회손 오류로 부터 안전하지 않다.
+ 네이티브 언어는 자바보다 플랫폼을 많이 타 이식성이 어렵다.
+ 디버깅도 어렵다.
+ 주의하지 않으면 속도가 더 느릴수 있다.
+ 가비지 컬렉터가 메모리를 회수 하지 못하고 심지어 추적할수도 없다. -> 아이템 8
+ 자바와 네이티브 메서드를 넘나들때 비용이 발생한다.
+ 네이티브 메서드와 자바코드 사이에 접착코드(glue code)를 작성 해야 하는데 귀찮은 작업이다.

<br><hr><br>

## 아이템 67. 최적화는 신중히 하라
+ 빠른 프로그램 보다는 좋은 프로그램을 작성하라.
+ 성능을 제한하는 설계를 피하라
+ API를 설계할 때 성능에 주는 영향을 고려하라.
+ 성능을 위해 API를 왜곡하는것은 않좋은 생각이다.
+ 각각 최적화 시도 전후 성능을 측정하라.

<br><hr><br>

## 아이템 68. 일반적으로 통용되는 명명 규칙을 따르라
+ [자바 명명 규칙](https://docs.oracle.com/javase/specs/jls/se18/jls18.pdf)
### 철자규칙
1. 패키지, 모듈
   + (.)으로 구분지어 계층화
   + 모두 소문자 알파벳 또는 숫자로
   + 외부 사용패키지의 경우 인터넷 도메인 역순으로 지정
   + 의미가 통하는 8글자 이하의 단어로 짓는다.
2. 클래스, 인터페이스
   + Pascal Case로 작성
   + 널리 통용되는 단어가 아니면 줄이지 않는다.
   + 약자는 첫문자만 대문자로 하자 -> HttpUrl
3. 메서드, 필드
   + Camel Case로 작성
   + 상수 필드는 예외로 모두 대문자로 작성하고 (_)로 구분한다.
### 문법 규칙
+ 타입을 바꿔서 다른 타입의 객체를 반환하는 역할을 한다면 toString, toArray
+ 객체의 내용을 다른 뷰로 보여주는 메서드는 asList, asType
+ 객체의 값을 기본형(primitive) 타입으로 반환한다면 intValue
+ 정적 팩터리라면 from, of, valueOf, newInstance, getType 등을 흔히 사용한다.