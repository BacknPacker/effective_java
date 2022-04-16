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

### 빌더는 점층적 생성자보다 클라이언트 코드를 읽고 쓰기가 훨씬 간결하고, 자바빈즈보다 안전하다.

