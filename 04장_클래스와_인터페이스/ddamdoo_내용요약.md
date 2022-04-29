# Effective Java 4장 클래스와 인터페이스



## Item 15. 클래스와 멤버의 접근 권한을 최소화하라



잘 설계된 컴포넌트와 그렇지 못한 컴포넌트의 가장 큰 차이점은 클래스 내부 데이터와 구현 정보를 외부로부터 얼마나 잘 숨겼고 꼭 필요한 정보들만 공개를 했는가다. 잘 설계된 컴포넌트일수록 내부 구현을 완벽히 숨겨서 구현과 API를 분리한다. 이처럼 구현과 API가 분리되어있다면 사용자 입장에서는 API만 신경쓰면되고 그 결과에만 집중하면된다.  이런 개념을 `정보 은닉`, 혹은 `캡슐화`라고 한다.

정보은닉의 장점은 대부분 컴포넌트를 서로 독립시켜 개발,테스트, 최적화, 적용, 분석, 수정을 개별적으로 할 수 있게 하는것과 연관되있다. 



### 정보 은닉(캡슐화)의 장점

* 시스템 개발 속도를 높힌다.
  * 여러 컴포넌트(개발, 테스트, 최적화...)가 개별적으로(병렬로) 개발 가능하기 때문이다.
* 시스템 관리 비용을 낮춘다. 
  * 각 컴포넌트를 더 빨리 파악하여 디버깅이 가능하고 컴포넌트 교체도 부담이 적기 때문이다.

* 성능 최적화에 도움을 준다.
  * 정보 은닉 자체가 성능을 높혀주는 것은아니지만, 완성된 시스템에서 최적화 할 컴포넌트를 정하고 다른 컴포넌트에 영향을 주지 않고 해당 컴포넌트만 최적화 할 수 있기 때문이다.

* 소프트웨어 재사용성을 높힌다.
  * 외부에 거의 의존하지 않고 독자적으로 동작이 가능한 컴포넌트는 다른 시스템에서도 바로 적용이 가능하기 때문이다.
* 큰 시스템을 제작하는 난이도를 낮춰준다.
  * 시스템이 완성되지 않은 상태에서도 개별 컴포넌트의 동작을 검증할 수 있기 때문이다.



### 자바의 정보 은닉을 위한 장치

접근 제어 메커니즘을 이용해 클래스, 인터페이스, 멤버의 접근성(접근 허용 범위)를 명시한다. 각 요소의 접근성은 그 요소가 선언된 위치와 접근 제한자(private, protected, public)로 정해진다. 그리고 이런 접근 제한자를 활용하여 정보 은닉을 할 수 있다.



#### 모든 클래스와 멤버의 접근성을 가능한 한 좁혀야 한다.

소프트웨어가 올바르게 동작하는 한 가장 낮은 접근 수준을 부여해야 한다. 톱레벨 클래스와 인터페이스는 package-private, public 을 부여할 수 있다. public으로 할 경우 공개 API가 되며, package-private가 되면 해당 패키지 내에서만 쓸 수 있다.

패키지 외부에서 쓸 일이 없다면 package-private로 선언하는 게 좋다. API가 아닌 내부 구현이기에 수정에서 자유로워져서 클라이언트에 피해 없이 다음 릴리즈에서 수정, 교체, 제거가 가능하다. public으로 선언하여 공개 API로 사용할 경우 영원히 하위 호환을 위해 관리해줘야 하며 수정 변경이 힘들어진다. 



#### private static 중첩 이용

클래스를 private static으로 중첩시키면 바깥 클래스 하나에서만 접근할 수 있다. 

클래스의 공개 API를 설계 후 모든 멤버를 private으로 만들고, 같은 패키지 내부의 다른 클래스가 접근해야 하는 멤버에 한하여 package-private으로 풀어주면 API 영향을 주지 않으면서 접근성을 좁힐 수 있다.



##### 접근제어자의 접근 가능성

* |                 | 같은 클래스 안에서 접근 | 같은 패키지 안에서 접근 | 상속받은 클래스에서 접근 | 외부에서 접근 |
| --------------- | :---------------------: | :---------------------: | :----------------------: | :-----------: |
| public          |            O            |            O            |            O             |       O       |
| protected       |            O            |            O            |            O             |       X       |
| package-private |            O            |            O            |            X             |       X       |
| private         |            O            |            X            |            X             |       X       |



####  멤버 접근성을 좁히지 못하는 제약

상위 클래스의 메소드를 하위 클래스에서 재정의하는 경우 이 메소드의 접근 수준을 상위 클래스보다 좁게 설정할 수 없다. 상위 클래스에서 package-private로 선언된 메소드는 하위 클래스에서는 package-private보다 높은 접근성인 protected, public을 사용할 수 있고 더 좁은 범위인 private는 사용할 수 없다. 그 이유는, 상위 클래스의 인스턴스는 하위 클래스의 인스턴스로 대체해 사용 가능해야 한다라(리스코프 치환 원칙)때문이다.  이 원칙을 어길 경우 컴파일 오류가 발생한다. 

만약, 테스트 목적으로 접근성을 높히고 싶다면, package-private까지는 허용이 된다. 패키지 내부에 테스트 클래스 내부에 작성해서 접근할 수 있기 때문이다. 하지만 그 이상으로 높혀서 공개 API가 되서는 안된다. 



#### public 클래스의 인스턴스 필드는 되도록 public이 아니어야 한다. 

모든 인스턴스 필드 낮은 접근성을 유지하는 것이 좋다. 

* 필드를 public으로 사용하거나, 가변 객체를 참조하거나, final이 아니면 필드 값을 제한할 수가 없어지며 해당 필드에 대한 모든 것은 불변식을 보장할 수 없게 된다.
* 필드가 수정될 때 막을 수 없기에 public 가변 필드를 갖는 클래스는 일반적으로 스레드 안전하지 않다.

* final이면서 불변 객체를 참조하더라도 내부 구현을 바꾸지 못해 리팩토링을 할 수 없다.

하지만 해당 클래스의 추상 개념을 완성하는데 필요한 구성요소로써의 상수라면 public static final 필드로 공개해도 좋다. 보통 관례적으로 대문자 알파벳으로 작성하며 구분은 밑줄(_)으로 한다. 이런 필드가 가변객체를 참조하면 final이 아닌 필드에서 적용되는 불이익이 모두 적용되기 때문에 반드시 기본 타입 값이나 불변 객체를 참조해야 한다. 



#### 자바 9의 모듈 시스템

자바 9에서는 모듈 시스템이라는 개념이 도입되면서 두 가지 암묵적 접근 수준이 추가되었다. 패키지가 클래스의 묶음이듯, 모듈은 패키지들의 묶음이다. 모듈은 자신에 속하는 패키지 중 공개 것들을 선언한다. protected나 public 멤버라도 해당 패키지를 공개하지 않았다면 외부에서 접근 할 수 없다.

하지만, 모듈에 적용되는 접근 수준은 모듈의 JAR 파일을 자신의 모듈 경로가 아니라 애플리케이션 classpath에 두면 그 모듈 안의 모든 패키지는 모듈이 없는 것 처럼 행동하기 때문에  모듈 공개여부와 상관없이 public 클래스가 선언한 모든 public과 protected 멤버를 모듈 밖에서 접근할 수 있게 된다. 

또한 모듈을 제대로 사용하기 위해서는 패키지를 모듈 단위로 묶고, 모듈 선언에 패키지들의 모든 의존성 명시해야하고, 소스 트리 재배치 뒤, 모듈 안으로부터 일반 패키지로의 모든 접근에 조치를 취해야 한다. 



---



## Item 16. public 클래스에서는 public 필드가 아닌 접근자 메소드를 사용하라.



```java
public class Point {
    private double x;
    private double y;

    public Point(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double getX() { return x; }
    public void setX(double x) { this.x = x; }
    public double getY() { return y; }
    public void setY(double y) { this.y = y; }
}
```

객체지향 프로그래머는 필드를 private으로 사용하고, public 접근자를 이용해서 접근하도록 한다. 패키지 바깥에서 접근할 수 있는 클래스라면 접근자를 통해 접근하고, 내부 표현 방식을 언제든 바꿀 수 있도록 유연성을 가지게 한다.

하지만 package-private이나 private 중첩 클래스는 데이터 필드를 노출하더라도 해당 클래스가 표현하고자 하는 추상 개념만 제대로 표현해주면 된다. 클라이언트 코드가 이 클래스 내부 표현에 묶이게 되지만, 클라이언트가 이 클래스를 포함하는 패키지 안에서만 동작하기에 문제가 되지 않는다. 



---



## Item 17. 변경 가능성을 최소화하라.

불변 클래스란 인스턴스의 내부 값을 수정할 수 없는 클래스이다. 자바 플랫폼 라이브러리에 String, 기본 타입의 박싱된 클래스들, BigInteger, BigDecimal이 그렇다.



#### 불변 클래스를 만들기 위한 규칙

* ##### 객체의 상태를 변경하는 메소드(변경자)를 제공하지 않는다. (ex: setter)

* ##### 클래스를 확장할 수 없도록 한다.(ex : final 클래스로 선언하거나 정적 팩토리 메소드)

* ##### 모든 필드를 final로 선언한다.

* ##### 모든 필드를 private로 선언한다.

  * public final도 불변이 되지만, 다음 릴리즈에서 내부 표현을 바꾸지 못하기에 권장하지 않는다.

* ##### 자신 외에는 내부의 가변 컴포넌트에 접근할 수 없도록 한다.

  * 클래스에 가변 객체가 하나라도 있다면 클라이언트에서 객체의 참조를 얻을 수 없도록 해야 한다. 접근자 메소드가 해당 필드를 그대로 반환해도 안되며, 생성자, 접근자, readObject 메소드 모두에서 방어적 복사를 수행해야 한다.



### 불변클래스 장점 & 단점

#### 장점

- ##### 불변 객체는 단순하다. 

  * 생성된 시점의 상태를 파괴될 때까지 그대로 가지고 있다.

* ##### 스레드 안전하며 따로 동기화할 필요가 없다.
  * 여러 스레드가 동시에 사용해도 절대 훼손되지 않는다. 스레드를 안전하게 만드는 가장 쉬운 방법이 불변 클래스다. 이처럼 스레드에 영향을 주지 않기 때문에 불변 객체를 공유해도 문제가 생기지 않는다.

* ##### 불변 객체는 자유롭게 공유 가능하고, 불변 객체 끼리 내부 데이터를 공유할 수 있다.
  * 불변 객체는 공유에서 자유로워 방어적 복사도 필요가 없다. 따라서 불변 클래스는 clone 메소드나 복사 생성자를 제공하지 않는게 좋다. 

* ##### 객체 생성시 불변 객체를 사용할 경우 이점이 많다.

  - 생성될 객체가 복잡한 구조라도 구성요소로 불변 객체가 사용된다면 불변식을 유지하기 훨씬 수월하기 때문에 맵의  키와 집합(Set)의 원소로 쓰기 좋다. 

* ##### 불변 객체는 그 자체로 실패 원자성을 제공한다.

  ###### ※ 실패원자성 : 메소드에서 예외가 발생한 후에도 그 객체는 계속 유효한 상태여야 한다는 성질

  * 상태가 변하지 않기에 잠깐이라도 불일치 상태에 빠지지 않는다. 

##### 
#### 단점

* ##### 값이 다르면 반드시 독립된 객체로 만들어야한다. 
  * BigInteger에서 한 개의 bit만 수정하려고 해도 결국 BigInteger 인스턴스가 새롭게 생성되어야 한다. 객체 자체가 큰 용량을 가지고 있다면 성능 문제가 발생할 수 있다.
    * 대안책 : 다단계 연산(multistep operation)들을 예측해 기본 기능으로 제공하기
    * BigInteger는 모듈러 지수같은 다단계 연산 속도를 높혀주는 가변 동반 클래스(companion class)를 package-private로 두고 있다.
    * 다른 예로는 String과 String의 가변 동반 클래스인 StringBuilder가 있다.



#### 불변 클래스를 만드는 설계 방법

클래스가 불변임을 보장하기 위해서는 자기 자신을 상속하지 못하게 해야 한다. 이 방법에는 두 가지가 있다.

* 클래스를 final로 선언하는 방법
* 모든 생성자를 private 또는 package-private로 만들고 public 정적 팩토리를 제공하는 방법  



불변 클래스 규칙을 모두 정하게 되면 과한 부분이 있어서 성능을 위해 완화할 때도 있다. 어떤 불변 클래스는 계산 비용이 큰 값을 final 이 아닌 필드에 캐싱하여 사용한다.



#### 불변 클래스 내용 정리

* ##### getter 메소드가 있다고 해서 무조건 setter 를 만들지는 말자.
  * 클래스는 꼭필요한 경우가 아니라면 불변이어야 한다.

* ##### 불변으로 만들 수없는 클래스라도 변경할 수 있는 부분을 최소한으로 줄이자.
  * 꼭 변경해야 할 필드를 뺀 나머지는 final로 선언해야 한다.
  * 합당한 이유가 없다면 모든 필드는 private final이어야 한다.

* ##### 생성자는 불변식 설정이 모두 완료된 ,초기화가 완벽히 끝난 상태의 객체를 생성해야한다.
  * 확실한 이유가 없다면 생성자와 정적 팩토리 외에는 그 어떤 초기화 메소드도 public으로 제공하면 안된다.



---



## Item 18. 상속보다는 컴포지션을 사용하라



상속은 코드 재사용을 활용하는 좋은 수단이지만 항상 최선은 아니다. 상속을 써도 문제가 없는 경우에는 아래 두 가지 방법 밖에없다.

* 상위 클래스와 하위 클래스가 모두 통제되는 패키지 안에 있을 경우
* 확장할 목적으로 설계되었고 문서화도 잘 된 클래스



상속은 메소드 호출을 하는 방식과는 다르게 캡슐화를 깨뜨린다.

```java
public class InstrumentedHashSet<E> extends HashSet<E> {
    private int addCount = 0;

    public InstrumentedHashSet() {
    }

    public InstrumentedHashSet(int initialCapacity, float loadFactor) {
        super(initialCapacity, loadFactor);
    }

    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }

    public int getAddCount() {
        return addCount;
    }

    public static void main(String[] args) {
        InstrumentedHashSet<String> s = new InstrumentedHashSet<>();
        s.addAll(Arrays.asList("1", "2", "3"));

        System.out.println(s.getAddCount());
    }
}

```

위와 같이 클래스를 구현하고, 아래 프린트문을 실행하게 되면 우리가 원하는 값인 3이 아니라 6을 반환하게 된다.

##### ![1651211032204](ddamdoo_내용요약.assets/1651211032204.png)

그 이유는 HashSet 의 addAll 이란 메소드에서 add 메소드를 사용하기 때문에 addAll과  add 모두 실행되어서 2씩 증가된 것이다.



##### 내부 구현 방식의 문제점

HashSet 문서에는 당연히도 쓰여있지 않고, addAll을 재정의하지 않으면 고칠 수 있는 문제다.

* add 메소드만 재정의하여 addCount를 증가시키는것도 다음 릴리즈에서 HashSet의 내부 구현이 어떻게 변경되냐에 따라 깨지기 쉽다.
* 하위 클래스에서 상위 클래스의 private 필드를 써야 하는 경우 구현이 불가능하다.
* 또한 addAll이나 add가 아니라 새로운 메소드를 추가해 기능을 넣는다면, 문제가 없어보이고 실제로도 위 재정의 방법보다는 안전하지만, 만약 새로운 릴리즈에서 추가된 메소드가 시그니처는 같고 반환타입은 다른 경우에는 또 문제가 발생한다. 

이러한 문제를 해결하기 위해 사용하는 방법이 `컴포지션`이다.



#### 컴포지션

기존 클래스가 새로운 클래스의 구성요소로 쓰이는 설계

새로운 클래스에서 기존 클래스에 대응하는 메소드를 호출하면 새로운 클래스는 기존 클래스의 메소드를 호출해서 결과를 반환하는데 이를 `전달(forwarding)`이라 하며, 이런 새로운 클래스의 메소드를 `전달 메소드(forwarding method)`라 부른다. 

```java
import java.util.*;

class CompositionInstrumentedSet<E> extends ForwardingSet<E>{
    private int addCount = 0;

    public CompositionInstrumentedSet(Set<E> s) {
        super(s);
    }

    @Override
    public boolean add(E e) {
        addCount ++;
        return super.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> collection) {
        addCount += collection.size();
        return super.addAll(collection);
    }

    public int getAddCount() {
        return addCount;
    }
}

class ForwardingSet<E> implements Set<E> {
    private final Set<E> s;

    public ForwardingSet(Set<E> s) {
        this.s = s;
    }

    @Override
    public int size() { return s.size(); }
    @Override
    public boolean isEmpty() { return s.isEmpty(); }
    @Override
    public boolean contains(Object o) { return s.contains(o); }
    @Override
    public Iterator<E> iterator() { return s.iterator(); }
    @Override
    public Object[] toArray() { return s.toArray(); }
    @Override
    public <T> T[] toArray(T[] ts) { return s.toArray(ts); }
    @Override
    public boolean add(E e) { return s.add(e); }
    @Override
    public boolean remove(Object o) { return s.remove(o); }
    @Override
    public boolean containsAll(Collection<?> collection) { return s.containsAll(collection); }
    @Override
    public boolean addAll(Collection<? extends E> collection) { return s.addAll(collection); }
    @Override
    public boolean retainAll(Collection<?> collection) { return s.retainAll(collection); }
    @Override
    public boolean removeAll(Collection<?> collection) { return s.removeAll(collection); }
    @Override
    public void clear() { s.clear(); }
}

class CompositionApplication {
    public static void main(String[] args) {
        CompositionInstrumentedSet<String> s = new CompositionInstrumentedSet<String>(new HashSet<>());

        s.addAll(Arrays.asList("1", "2", "3"));

        System.out.println(s.getAddCount());
    }
}
```

##### ![1651213103886](ddamdoo_내용요약.assets/1651213103886.png)

추가 회수를 저장하는 클래스인 InstrumentedSet과 HashSet에 있는 모든 기능을 정의한 Set 인터페이스를 구현했기에 견고하고 유연하다. 컴포지션은 한 번만 구현해두면 사용한 인터페이스의 어떠한 구현체에도 적용이 가능하다. 또한 기존 클래스 내부 구현방식의 영향에서 벗어나며, 기존 클래스의 새로운 메서드가 추가되더라도 전혀 영향받지 않는다.



래퍼 클래스는 다른 인스턴스를 감싸고 있는 클래스로, 데코레이터 패턴이라고 부른다. 래퍼 클래스는 단점이 거의 없고, **콜백 프레임워크와 어울리지 않는다**는 점만 주의하면 된다.

콜백 프레임워크에서는 자기 자신의 참조를 다른 객체에 넘겨 다음 호출(콜백) 때 사용하도록 한다. 내부 객체는 자신을 감싸고 있는 래퍼의 존재를 모르니 대신 자신(this)의 참조를 넘기고, 콜백 때는 래퍼가 아닌 내부 객체를 호출하게 되는데 이를 **SELF문제**라 한다. 전달 메서드, 래퍼 객체가 메모리 사용량에 비용 낭비가 생기지 않나 걱정할 수 있지만, 실제로는 둘 다 별다른 영향을 끼치지 않는다. 



상속은 하위클래스가 상위 클래스의 진짜 하위 타입인 경우에만 사용되야 한다. 클래스들의 관계가 `is-a` 관계라고 확신할 수 없다면 상속하지 말아야 한다.

