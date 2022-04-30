# 4장. 클래스와 인터페이스

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
  Collections.unmodified(Arrays.asList(PRIVATE_VALUES));
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


## 아이템 16. public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라.
### package-private 클래스 혹은 private 중첩 클래스라면 데이터 필드를 노출한다해도 문제가 없다.
+ 가변 필드를 노출해서는 안되며, 불변 필드라도 안심할 수 없다.
+ 불변 필드 노출사례: java.awt.package의 Point, Dimension 클래스


## 아이템 17. 변경 가능성을 최소화하라.
### 클래스를 불변으로 만드는 규칙
1. 객체의 상태를 변경하는 메서드(변경자)를 제공하지 않는다.
2. 클래스를 확장할 수 없도록 한다.
3. 모든 필드를 final로 선언한다.
4. 모든 필드를 private로 선언한다.
  + public final로만 선언하면, 다음 릴리스에서 내부 표현을 바꿀 수 없으므로 권장하지 않는다.
5. 자신 외에는 내부 가변 컴포넌트에 접근할 수 없도록 한다.
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


## 아이템 18. 상속보다는 컴포지션을 사용하라.
### 클래스를 불변으로 만드는 규칙