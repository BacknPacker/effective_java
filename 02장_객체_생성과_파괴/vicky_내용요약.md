# 2장. 객체 생성과 파괴
+ 미래의 수정과 개선이 수월한 코드를 작성하자.
+ 객체를 생성해야 할 때와 하지 말아야 할 때를 구분하는 법 <br>
  올바른 객체 생성과 불필요한 생성을 피하는 방법 <br>
  제 때 파괴하고 파괴 전 수행해야 할 작업을 관리


## 아이템1. 생성자 대신 정적 팩터리 메서드를 고려하라.

### 클라이언트가 클래스의 인스턴스를 얻는 전통적인 수단은 public 생성자다.
[생성자 예시](http://www.tcpschool.com/java/java_methodConstructor_constructor)
```Java
Car(String modelName, int modelYear, String color, int maxSpeed) {
    this.modelName = modelName;
    this.modelYear = modelYear;
    this.color = color;
    this.maxSpeed = maxSpeed;
    this.currentSpeed = 0;
}
```
```Java
Car myCar = new Car("아반떼", 2016, "흰색", 200); // 생성자의 호출
```

### 정적 팩터리 메소드 (정적 메소드)
[정적 팩터리 메소드 예시](https://7942yongdae.tistory.com/147)
```Java
private Car(String brand) { 
  this.brand = brand; 
} 
public static Car brandOf(String brand) {
  return new Car(brand); 
}
```
```Java
Car car = Car.brandOf("현대");  // 정적 팩토리 메소드를 활용한 객체 생성
```
```Java
// 본문사례, Boolean 객체참조로 변환, Wrapper Class화
public static Boolean valueOf(boolean b) {
  return b? Boolean.TRUE : Boolean.FALSE;  
}
```

### 팩터리 메서드 패턴이란?
+ GoF 디자인 패턴의 생성패턴 중 하나
+ 객체 생성 처리를 서브 클래스로 분리해 처리하도록 캡슐화하는 패턴을 말한다.

[팩터리 패턴 예시](https://jdm.kr/blog/180)
```Java
public abstract class RobotFactory {
	abstract Robot createRobot(String name);
}
```
```java
public class SuperRobotFactory extends RobotFactory {
	@Override
	Robot createRobot(String name) {
		switch( name ){
			case "super": return new SuperRobot();
			case "power": return new PowerRobot();
		}
		return null;
	}
}
```
+ 생성자를 대신하는 정적 팩토리 메소드는 클래스 객체를 생성하기 위한 것이라고 한다면,
+ 팩터리 메서드는 추상화 메서드를 이용해서 객체 생성을 구현하기 위한 하나의 패턴 관점!

### 정적 팩터리 메서드의 장점
##### 1. 반환 객체 특성을 제대로 묘사하는 이름을 지을 수 있다.
+ BigInteger(int,int,Random) vs BigInteger.probablePrime
+ 동일한 시그니처를 가지는 메서드를 만들고자 할 때
##### 2. 호출시마다 인스턴스를 새로 생성하지 않아도 된다.
+ 인스턴스 통제 클래스
+ 인스턴스를 통제하면 클래스를 싱글턴으로 만들 수도, 인스턴스화 불가로 만들 수도 있다. 
+ 또한 불변 값 클래스에서 동치인 인스턴스가 단 하나뿐임을 보장할 수 있다.
+ 인스턴스 통제는 플라이웨이트 패턴의 근간이 되며, 열거타입은 인스턴스가 하나만 만들어짐을 보장한다.
##### 3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.
+ API를 작게 유지할 수 있다. 예, java.util.Collections
+ 인터페이스 기반 프레임워크를 만드는 핵심기술
+ [자바8] 인터페이스가 정적 메서드를 가질 수 있게 되었다.
+ [자바9] private 정적 메서드까지 허용. 정적 필드와 정적 멤버 클래스는 여전히 public이어야 한다.
##### 4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.
+ EnumSet 클래스가 원소수에 따라 RegularEnumSet, JumboEnumSet 인스턴스를 반환
+ 클래스는 팩터리가 건네주는 객체가 어느 클래스의 인스턴스인지 알 수도 없고 알 필요도 없다.
##### 5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.
+ 서비스 제공자 프레임워크 생성 근간, JDBC
    + 서비스 인터페이스, 제공자 등록 API, 서비스 접근 API, (서비스 제공자 인터페이스) 
    + Connection, DriverManager.registerDriver, DriverManager.getConnection, Driver
+ 브리지 패턴
+ 의존 객체 주입 프레임워크

### 정적 팩터리 메서드의 단점
##### 1. 상속을 하려면 public이나 protected 생성자가 필요하니 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없다.
+ 컬렉션 프레임워크의 유틸리티 구현 클래스들은 상속할 수 없다.
+ 상속보다 컴포지션 사용, 불변타입으로 만들기 위한 제약
##### 2. 찾기가 어렵다.
+ 생성자처럼 API 설명에 명확하게 들어나지 않음으로 명명 방식들을 확인하자.

<hr>

##### TIP
+ 정적 팩토리 메소드를 사용하는 경우: Entity, DTO로 변환하는 관계
+ 생성자를 사용하는 경우: 단순히 변수들을 주입할 때
<hr>


## 아이템2. 생성자에 매개변수가 많다면 필터를 고려하라.

### 점층적 생성자 패턴의 한계
+ 매개변수가 많을 때, 클라이언트 코드를 작성하거나 읽기 어렵다.

### 자바빈즈 패턴
+ 여러 개의 set 메서드가 필요
+ 객체가 완전히 생성되기 전에는 일관성이 무너진 상태에 놓이게 된다.
+ 버그를 심은 코드와 그 버그 때문에 런타임에 문제를 겪는 코드가 물리적으로 멀리 떨어져 있을 것이므로 디버깅도 어렵다.
+ 불변으로 만들기 어렵고, 추가 스레드 작업 필요
+ freeze 메서드

### 빌더 패턴
+ 필수 매개변수를 가지는 생성자로 빌더 객체를 얻고, build() 호출로 필요한 클래스 객체를 얻는다.
```java
// 본문 예제
public static class Builder {
  private final int servingSize;
  private final int servings;

  private final int calories = 0;
  private final int fat = 0;
  
  public Builder(int servingSize, int servings) {
    this.servingSize = servingSize;
    this.servings = servings;
  }
  
  public Builder caleries(int val) {
    calories = val;
    return this;
  }
  
  public Builder fat(int val) {
    fat = val;
    return this;
  }

  public NutritionFacts(Builder builder) {
    servingSize = builder.servingSize;
    servings = builder.servingSize;
    calories = builder.caleries;
    fat = builder.fat;
  }
}
```
```java
NutritionFacts cocaCola = new NutritionFacts.Builder(240,8)
    .calories(100).builder();
```
+ 명확하고 읽기 쉬운 코드를 작성할 수 있다.
+ 파이썬과 스칼라의 명명된 선택적 매개변수를 모방
+ 매개변수 확인을 위한 IllegalArgumentException


+ 불변? 어떤 변경도 허용하지 않으나 제한적 조건 내에서의 변경은 허용한다.

### 계층적으로 설계된 클래스와 잘 어울리는 빌더 패턴
```java
// 본문 예제
public abstract class Pizza {
	public enum Topping {HAM, MUSHROOM, ONION}
	final Set<Topping> toppings;

	abstract static class Builder<T extends Builder <T>> {
	EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
	Public T addTopping(Topping topping) {
	toppings.add(Objects.requireNonNull(topping));
	return self();
}
abstract Pizza build();

// 하위 클래스는 이 메서드를 재정의하여
// “this”를 반환하도록 해야 한다.
protected abstract T self();
}
Pizza(Builder<?> builder) {
	Toppings = builder.toppings.clone();
}
}
```
```java
public class NyPizza extends Pizza {
	public enum Size {SMALL, MEDIUM, LARGE}
	private final Size size;
	public static class Builder extends Pizza.Builder<Builder> {
	private final Size size;
	public Builder(Size size) {
	this.size = Objects.requireNonNull(size);
}
@Override public NyPizza build(){
	return new NyPizza(this);
}
@Override protected Builder self(){return this;}
}
Private NyPizza(Builder builder) {
	super(builder);
	size = builder.size;
}
}
```
+ 셀프타입 관용구
+ 공변반환 클래스
  + 하위 클래스의 메서드가 상위 클래스의 메서드가 정의한 반환 타입이 아닌, 그 하위 타입을 반환하는 기능

### 빌더패턴 사용시 고려사항
+ 빌더 생성시 매개변수가 4개 이상은 되야 값어치를 한다.
+ API는 시간이 지날수록 매개변수가 많아짐을 고려해야 한다.

##### ❗ 빌더는 점층적 생성자보다 클라이언트 코드를 읽고 쓰기가 훨씬 간결하고, 자바빈즈보다 안전하다.


## 아이템3. Private 생성자나 열거 타입으로 싱글턴임을 보증하라.

### 싱글턴
+ 인스턴스를 오직 하나만 생성할 수 있는 클래스
+ 함수와 같은 무상태 객체나 설계상 유일해야 하는 시스템 컴포넌트
+ 싱글턴 패턴은 클라이언트 테스트의 한계를 갖는다.

### 1. public static final 필드 방식
```java
public class Elvis {
  public static final Elvis INSTANCE = new Elvis();
  private Elvis(){…}
  public void leaveTheBuilding(){…}
}
```
+ private 생성자는 처음 Elvis.INSTANCE 초기 생성시에만 처음 호출된다.
+ 권한 있는 클라이언트의 AccesibleObject.setAccessible private 생성자 호출 우려
  + 두 번째 객체 생성 전 예외처리

### 2. public static 멤버 방식
```java
public class Elvis {
  private static final Elvis INSTANCE = new Elvis();
  private Elvis();
  public static Elvis getInstance(){ return INSTANCE; }
  public void leaveTheBuilding(){…}
  private Object readResolve(){
      return INSTANCE;
  }
}
```
##### 이렇게 싱글턴 생성시 장점
+ 싱글턴을 변경하고자 하는 경우 호출하는 인스턴스 변경이 되므로 수정시 편리
+ 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있다
+ 정적 팩터리의 메서드 참조를 공급자로 사용할 수 있다.

### 싱글턴의 직렬화
+ 모든 인스턴스를 일시적이라고 선언하고 readResolve 메서드를 제공해야 한다.
+ 이렇게 하지 않으면. 직렬화된 인스턴스를 역직렬화할 때마다 인스턴스가 새로 생성됨

### 열거타입의 싱글턴
```java
public enum Elvis {
  INSTANCE;
  public void leaveTheBuildings(){…}
}
```
+ 대부분의 상황에서 원소가 하나뿐인 열거타입이 싱글턴을 만드는 가장 좋은 방법이다.
+ 단 만드려는 싱글턴이 enum 이외의 다른 클래스를 상속해야 한다면 이 방법은 사용할 수 없다.
+ 열거타입이 다른 인터페이스를 구현하도록 선언할 수는 있다.


## 아이템4. 인스턴스화를 막으려거든 private 생성자를 사용하라.

### 정적 메서드와 정적 필드만을 담을 클래스를 만드는 경우
+ java.lang.Math, java.util.Arrays, java.util.Collections
+ final 클래스와 관련된 메서드를 모아두는 경우
+ 정적 메서드만 담은 유틸리티 클래스는 인스턴스로 만들어 쓰려고 설계한 게 아니다.
+ 인스턴스화 방지용이라는 주석을 달자
+ 하위 클래스에서 상위 클래스 생성자에 접근할 수 없게 된다.


## 아이템5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라.

### 맞춤법 검사기에서 여러 사전을 사용할 수 있도록 하려면?
+ 사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글턴 방식이 적합하지 않다.
```java
public class SpellChecker {
  Private final Lexicon dictionary;
  Public SpellChecker(Lexion dictionary){
  this.dictionary = Objects.requiredNonNull(dictionary);
  }
  public Boolean isValid(String word){}
  public List<String> suggestions(Strinf typo){}
}
```
+ 불변을 보장, 생성자, 정적 팩터리, 빌더 모두 응용 가능하다
+ 팩터리 메서드의 구현
+ But, 의존성이 매우 많은 경우에는 오히려 코드가 복잡해질 수 있다.
+ 의존 객체 프레임워크 (Spring, Dagger, Guice)


## 아이템6. 불필요한 객체 생성을 피하라.

### 객체를 new로 선언해서 불필요한 객체를 생성하지 말고, 바로 값을 대입해 하나의 불변 객체를 생성하자.
+ 같은 가상머신 안에서 이와 같은 문자열 리터럴을 사용하는 모든 코드가 같은 객체를 재사용함이 보장된다.

### 생성자는 호출될 때마다 새로운 객체를 만들지만 팩터리 메서드는 아니다.

### 정규표현식용 Pattern 인스턴스는 한 번 사용 후, GC 대상이므로 인스턴스 생성비용이 높다.
+ 캐싱을 이용하자. 메서드를 통해 만들어 놓은 인스턴스를 재사용하도록 한다.
+ 지연초기화는 권하지 않는다.

### Map 인터페이스의 keyset 메서드는 매번 같은 Set 인스턴스를 반환할 수도 있다.

### 불필요한 오토박싱을 주의하자
+ long 타입을 Long 타입에 더하면서 불필요한 Long 타입 인스턴스가 생성된다.
+ 박싱된 기본타입보다는 기본 타입을 사용하고, 의도치 않은 오토박싱이 숨어들지 않도록 주의하자.

### 요즘 JVM GC는 상당히 잘 최적화 되어 가벼운 객체용을 다룰 때에는 직접 만든 객체 풀보다 훨씬 빠르다.

### 방어적 복사에 실패하면 버그와 보안에 영향, 불필용한 객체 생성은 코드 형태와 성능에 영향을 준다.


## 아이템7. 다 쓴 객체 참조를 해제하라

### 메모리 누수는 성능저하로 이어질 수 있다.
+ 객체 참조 하나를 살려두면 GC는 그 객체 뿐만 아니라 그 객체가 참조하는 모든 객체를 회수하지 못한다.

### 메모리 누수가 발생하는 경우
##### 1. 자기 메모리를 직접 관리하는 클래스라면 프로그래머는 항시 메모리 누수에 주의해야 한다.
```java
public Object pop(){
  if (size==0)
    throws new EmptyStackException();
  Object result = elements[--size];
  elements[size] = null;
  return result;
}
```
+ null 처리는 예외적인 경우에만 수행하면, 일반적으로 scope 밖으로 다 쓴 참조를 해제하는 것이 바람직하다.
+ 배열의 비활성 영역이 되는 것을 null 처리해서 GC가 알 수 있도록 하자.

##### 2. 캐시의 메모리 누수
+ 외부에서 키를 사용하는 동안만 엔트리가 살아있도록 하려면 WeakHashMap을 사용하자.
+ 엔트리 청소: 백그라운드 스레드 활용하거나 새 엔트리를 추가할 때 부수 작업으로 수행

##### 3. 리스너 또는 콜백
+ weak reference 처리, WeakHashMap에 키로 저장


## 아이템8. finalizer와 cleaner 사용을 피하라.

### finalizer와 cleaner 사용의 문제점
+ finalizer 사용자제하고 cleaner를 대안으로 제시했으나 여전히 자바 라이브러리에서 finalizer가 사용된다.
+ cleaner 역시 예측할 수 없고, 느리고, 일반적으로 불필요하다.
+ 백그라운드에서 실행되며 둘 다 언제 실행될지 알 수 없다. JVM 알고리즘마다 차이가 발생
+ 상태를 영구적으로 수정하는 작업에서는 절대 의존해서는 안된다.
+ 동작 중 발생한 예외는 처리할 작업이 있더라도 그 순간 종료되거나 훼손된 객체를 사용하는 다른 스레드가 발생하는 문제가 발생할 수 있다.
+ 심각한 성능문제
+ 심각한 보안문제
  + 생성자나 직렬화 과정에서 예외가 발생하면, 생성되다 만 객체에서 악의적인 하위 클래스의 finalize가 수행될 수 있게 된다.,

### 대체방안 - AutoCloseable 구현
+ 객체를 다 사용했다면 close()를 호출하고, 이후에 객체가 불리는 경우 IllegalStateException을 던짐

### finalizer와 cleaner 사용하기
+ 안전망 역할, 클라이언트가 하지 않은 자원회수용
+ FileInputStream, FileOutputStream, ThreadPoolExecutor
+ 네이티브 피어와 연결된 객체
  + 일반 자바 객체가 네이티브 메서드를 통해 기능을 위임한 네이티브 객체
  + 성능저하를 감당할 수 있고, 심각한 자원을 가지고 있지 않는 경우만
+ cleaner는 public API에 나타나지 않는다
```java
public class Room implements AutoCloseable {
  private static final Cleaner cleaner = Cleaner.create();
  private static class State implements Runnable {
      int numJunkPiles;
      State(int numJunkPiles){
        this.numJunkPiles = numJunkPiles;
      }
    
    @Override public void run(){
      System.out.println(“방청소”);
      numJunkPiles = 0;
    }
  }

  private final State state;
  private final Cleaner.Cleanable cleanable;
  
  public Room(int numJunkPiles){
    State = new State(numJunkPiles);
    Cleanable = cleaner.register(this, state);
  }
  
  @Override public void close(){
      Cleanable.clean();
  }
}
```
+ cleaner는 안전망 역할이나 중요하지 않은 네이티브 자원 회수용으로만 사용하자. 
  + 이런 경우는 불확실성과 성능저하에 주의해야 한다.


## 아이템9. Try-finally보다는 try-with-resource를 사용하라.

### 자바에는 close 메서드를 직접 호출해야만 하는 자원이 많다.

### try-finally의 한계
+ 자원이 둘 이상인 경우 코드가 지저분해진다
+ 두 번째 예외적용으로 첫 번째 예외에 대한 정보를 남기지 않게 된다.

### 꼭 회수해야 하는 자원을 다룰때는 try-with-resource를 사용하자
```java
static void copy(String src, String dst) throws IOException {
  try(InputStream in = new FileInputStream(src);
      OutputStream out = new FileOutputStream(dst)) {
    Byte[] buf = new byte[BUFFER_SIZE];
    Int n;
    while((n=in.read(buf))>=0)
        out.write(buf,0,n);
  }
}
```
