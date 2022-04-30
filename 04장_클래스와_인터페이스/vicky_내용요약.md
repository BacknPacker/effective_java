# 4장. 클래스와 인터페이스
<br>

## 아이템 15. 클래스와 멤버의 접근권한을 최소화하라.
+ 정보은닉, 혹은 캡슐화라고 하는 개념은 소프트웨어 설계의 근간이 되는 개념이다.
### 캡슐화의 장점
+ 개발속도가 높아진다.
+ 관리비용 낮춘다.
+ 성능 최적화에 도움
+ 소프트웨어 재사용성을 높인다.
+ 개별 컴포넌트 동작 검증으로 개발 난이도를 낮춘다.
### 접근 제어 메커니즘, 접근제한자
+ 모든 클래스와 멤버의 접근권한을 가능한 한 좁혀야 한다.
+ 외부 패키지에서 쓸 일이 없다면, package-private 선언하자.
> 한 클래스에서만 사용하는 package-private 톱 레벨 클래스나 인터페이스는 이를 사용하는 클래스 안에 private static으로 중첩시켜보자. (아이템24)
+ private, package-private, protected, public
+ private와 pacakage-private 멤버는 모두 해당 클래스의 구현에 해당하므로 공개 API에 영향을 주지 않는다.
> Serialize을 구현하는 클래스에서는 그 필드들도 의도치 않게 공개 API가 될 수도 있다. (아이템 86, 87)
##### 상위 클래스 메서드 재정의 한계
+ 멤버 접근성을 좁힐 수 없다, 리스코프 치환 원칙
##### public 클래스의 인스턴스 필드는 되도록 public이 아니어야 한다.
+ 인스턴스 필드를 제한할 수 있어야 하기 때문
+ 심지어 필드가 final이면서 불변객체를 참조하더라도 문제는 여전히 남는다. 내부 구현을 바꾸고 싶어도 그 public 필드를 없애는 방식으로 리팩터링할 수 없게 된다.
+ 정적 필드에서의 예외
    + 해당 클래스의 구성요소로써 상수라면 public static final 필드로 공개
    + 반드시 기본타입이나 불변객체를 참조해야 한다
##### public static final 배열의 보안 허점
+ 길이가 0이 아닌 배열은 모두 변경가능하다.
1. 배열을 private으로 변경하고 public 불변 리스트를 추가
```java
private static final Thing[] PRIVATE_VALUES = {...};
public static final List<Thing> VALUES = 
  collections.unmodified(Arrays.asList(PRIVATE_VALUES));
```
2. 배열을 private로 변경하고 복사본을 반환하는 public 메서드를 추가
```java
private static final Thing[] PRIVATE_VALUES = {...};
public static final Thing[] value(){
        return PRIVATE_VALUES.clone();
}
```
##### 자바9 모듈시스템
+ 모듈은 패키지의 묶음
+ 공개할 것을 module-info.java에 선언
+ 모듈 접근제한자와 별개로 public 클래스에 선언한 public과 protected 멤버를 모듈 밖에서도 접근할 수 있게 한다.
+ 자바 라이브러리에서 공개하지 않은 패키지들은 해당 모듈 밖에서는 절대로 접근할 수 없다.
> 프로그램 요소의 접근성은 가능한 한 최소한으로 하라. 꼭 필요한 것들만 골라 최소한의 public API를 설계하자.
> 그 외에는 클래스, 인터페이스, 멤버가 의도치 않게 API로 공개되는 일이 없도록 해야 한다.
> public 클래스는 상수용 public static final 필드 외에는 어떠한 publix 필드도 가져서는 안 된다.
> public static final 필드가 참조하는 객체가 불변인지 확인하라.
<br>

## 아이템 16. public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라.
### package-private 클래스 혹은 private 중첩 클래스라면 데이터 필드를 노출한다해도 문제가 없다.
+ 가변 필드를 노출해서는 안되며, 불변 필드라도 안심할 수 없다.
+ 불변 필드 노출사례: java.awt.package의 Point, Dimension 클래스
<br>

## 아이템 17. 변경 가능성을 최소화하라.
### 클래스를 불변으로 만드는 규칙
+ 객체의 상태를 변경하는 메서드(변경자)를 제공하지 않는다.
+ 클래스를 확장할 수 없도록 한다.
+ 모든 필드를 final로 선언한다.
+ 모든 필드를 private로 선언한다.
  + public final로만 선언하면, 다음 릴리스에서 내부 표현을 바꿀 수 없으므로 권장하지 않는다.
+ 자신 외에는 내부 가변 컴포넌트에 접근할 수 없도록 한다.
  + 생성자, 접근자, readObject 메서드 모두에서 방어적 복사를 수행하라.
##### 함수형 프로그래밍: 피연산자는 그대로
+ 명명에 동사 대신 전치사를 사용한 점에 주목
```java
public Complex plus(Complex c){
  return new Complex(re + c.re, im + c.im);
}
```
##### 불변 객체는 근복적으로 스레드 안전하여 동기화 필요가 없다.
+ 불변 클래스라면 한 번 만든 인스턴스를 최대한 재활용하기를 권한다.
+ 복사의 불필요
+ 여러 클라이언트가 인스턴스를 공유하여 메모리 사용량과 가비지 컬렉션 비용이 줄어든다.
> 불변 객체는 그 자체로 실패 원자성을 제공한다. (아이템 76)
+ 반드시 독립된 객체로 값에 다르면 큰 비용으로 생성 필요
+ BigInteger, BigSet
+ getter가 있다고 setter를 반드시 만들지 말자.
+ CountDownLatch 클래스
	+ 가변 클래스이지만 인스턴스를 한 번 생성해 사용하고 카운트가 0에 도달해 재사용할 수 없다.
<br>

## 아이템 18. 상속보다는 컴포지션을 사용하라.
### 상속을 받는 하위클래스의 구현
+ 다른 패키지의 구체 클래스를 상속하는 일은 위험하다.
+ 릴리즈에 따라 달라지는 상위 클래스의 영향으로 하위 클래스의 구현을 변경해야 할 수 있음을 주의
+ 잘못된 구현의 예 18-1.
	+ HashSet의 addAll 메서드는 add 메서드를 사용해서 구현되어 있다. 구현 doc?
+ 상위 클래스의 메서드를 추가하는 경우는 하위 클래스에서 재정의 하지 못했을 때의 문제가 발생하게 된다.
##### 컴포지션과 전달을 사용하자. → 위임
+ 기존 클래스를 확장하는 대신, 새로운 클래스를 만들고 private 필드로 기존 클래스의 인스턴스를 참조하게 하자.
+ 다른 Set 인스턴스를 감싸고 있는 클래스를 래퍼 클래스라고 한다. 데코레이션 패턴.
	+ 콜백 프레임워크와의 사용에 주의하자.
##### 상속은 is-a의 상황에서의 클래스에서만 쓰여야 한다.
+ 컴포지션을 사용해야 할 상황에서 상속을 사용하게 되면, 불변식이 깨지게 된다.
+ 결함이 있는 API도 승계되도 괜찮은지를 고려하자.
<br>

## 아이템 19. 상속을 고려해 설계하고 문서화하라. 그러지 않으면 상속을 금지하라.
+ 메서드를 재정의하면 문서로 정리해서 남겨놓자. Implementation Requirements
+ 클래스의 내부 동작 과정 중간에 끼어들 수 있는 훅을 선별하여 protected 메서드 형태로 공개해야 할 수도 있다.
+ 상속용으로 설계한 클래스는 배포 전에 반드시 하위 클래스를 만들어 검증해야 한다.
### 상속의 금지
+ 상속을 금지하기 위해서는 클래스를 final로 선언하거나 생성자 모두를 외부에서 접근할 수 없도록 하자.
<br>

## 아이템 20. 추상클래스보다는 인터페이스를 우선하라.
+ 추상클래스가 정의한 타입을 구현하는 클래스는 반드시 하위 클래스가 되어야 한다.
### 왜 인터페이스를 우선해야 하는가?
+ 기존 클래스에도 새로운 인터페이스를 구현해 넣을 수 있다.
+ 믹스인 정의에 적합
	+ 주된 타입 외에도 특정 선택적 행위를 제공한다고 선언하는 효과
	+ 단일 상속만을 지원하는 클래스와 달리 인터페이스는 계층구조의 문제에서 자유로운 타입 프레임워크를 만들 수 있다.
	+ 래퍼 클래스와 함께 사용하면 안전하고 기능 향상에 도움이 된다.
### 인터페이스 디퐅트 메소드 java 8
+ @implSec 상속하려는 사람을 위한 문서화
```java
public interface Inter {
	public default void example(int n){
	System.out.println(“디폴트 메소드 예시”);
}
}
```
+ 다중상속시, 컴파일러는 상속받은 클래스의 디폴트 메소드를 @Override한 메소드보다 우선시해서 빌드 에러가 발생하지 않는다.
### AbstractInterface, 골격 구현 인터페이스
+ 인터페이스와 골격 구현 인터페이스를 함께 사용해서 인터페이스와 추상 클래스의 장점을 취할 수 있다.
+ 템플릿 메서드 패턴
### 단순 구현
+ Abstract Map.SimpleEntry
##### 인터페이스에 있는 구현상의 제약으로 골격 추상 클래스를 이용하는 경우가 더 흔하다.
<br>

## 아이템 21. 인터페이스는 구현하는 쪽을 생각해 설계하라.
### 인터페이스 메소드 추가 생성시 주의점
+ 새로운 디폴트 메소드를 추가하는 것이 불변식을 해칠 수도 있다.
+ 디폴트 메소드 생성시에는 주의를 기울이자.
<br>

## 아이템 22. 인터페이스는 타입을 정의하는 용도로만 사용하라.
##### 상수 인터페이스를 구현하는 행위는 API 노출로 이어진다.
+ 상수를 공개할 목적이라면, 인스턴스화를 방지하자.
```java
Package effectiveejava.chapter4;
Public class PhysicalConstants {
	private PhysicalConstants( ) { }	// 인스턴스화 방지
	public static final double AVOGADROS_NUMBER = 6.022_140_857e23;
}
``` 
##### 인터페이스는 타입을 정의하는 용도로만 사용해야 한다. 상수 공개용 수단으로 사용하지 말자.
<br>

## 아이템 23. 태그 달린 클래스보다는 클래스 계층구조를 활용하라.
+ 불필요한 코드가 많고, 가독성 또한 좋지 않다.
### 클래스 계층구조
+ 다만 사례에서 접근자는 공개 여부에 따라 고려해야 할 사항
```java
Abstract class Figure {
	abstract double area();
}
Class Circle extends Figure {
	Final double radius;
	Circle(double radius) { this.radius = radius; }
    
    @Override double area(){ return Math.PI * (radius * radius) }
}
Class Rectangle extends Figure {
	final double length;
	final double width;
	rectangle (double length, double width) { 
        this. length = length; 
        this. width = width; 
    }

    @Override double area( ) { return Math.PI * (radius * radius); }
}
```
<br>

## 아이템 24. 멤버 클래스는 되도록 static으로 만들라
### 중첩클래스
+ 정적 멤버 클래스, 비정적 멤버 클래스, 익명 클래스, 지역 클래스
+ 정적 멤버 클래스 제외 내부 클래스에 포함
##### 정적 멤버 클래스
+ 외부 클래스의 객체가 없어도 inner 클래스의 객체 생성이 가능
##### 비정적 멤버 클래스
+ 멤버 클래스에서 바깥 인스턴스에 접근할 일이 없다면 무조건 static을 붙여서 정적 멤버 클래스로 만들자.
+ 숨은 외부 참조는 시간, 공간의 소비, GC가 인스턴스 수거 처리가 어려움
##### 익명 클래스
+ 단발성 사용을 위한 클래스
+ 제약이 많은데 선언지점에서만 인스턴스를 만들 수 있고, 비정적인 문맥에서 사용될 때만 바깥 클래스의 인스턴스를 참조할 수 있다.
##### 지역 클래스
+ 정적 멤버는 가질 수 없고, 가독성을 위해 짧게 작성해야 한다.
### 결론
1. 메서드 내에서 정의하기 어려운 경우는 멤버 클래스로 만들자.
2. 멤버 클래스가 외부 인스턴스를 참조하는 경우에는 비정적으로
3. 그렇지 않다면 정적으로
4. 한 메서드 안에서만 쓰이고, 생성 지점이 단 한 곳이라면 익명 클래스로 그렇지 않다면, 지역 클래스로 만들자. 
<br>

## 아이템 25. 톱레벨 클래스는 한 파일에만 담으라
+ 어떤 소스 파일을 먼저 건네느냐에 따라 동작이 달라진다.
+ 구지 톱레벨 클래스를 한 파일에 담고 싶다면, 정적 멤버 클래스를 사용하자.
```java
public class Test {
    public static void main(String main[] args){
        System.out.println(Utensil.Name + Dessert.Name);
    }
    private static class Utensil {
        static final String Name = "pan";
    }
    
    private static class Dessert {
        static final String Name = "cake";
    }
}
```
