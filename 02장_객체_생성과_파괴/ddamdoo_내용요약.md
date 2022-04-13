# Effective Java 2장 객체 생성과 파괴



## Item 1. 생성자 대신 정적 팩토리 메서드를 고려하라.

클래스에서 인스턴스를 얻는 전통적인 수단은 public 생성자를 이용하는 방식이다.

하지만 public 생성자를 대신하거나 함께 정적 팩토리 메소드를 제공할 수 있다.



### 정적 팩토리 메소드를 사용할 때의 장점



#### 1. 이름을 가질 수 있다.

생성자와 생성자에서 사용되는 파라미터로는 반환될 객체에 대해서 제대로 설명하기 어렵다. 

아래에 코드를 보면

```java
class Book {
    private String title;

    public Book() {
    }

    public Book(String title) {
        this.title = title;
    }

    public static Book withTitle(String title) {
        return new Book(title);
    }

    public static void main(String[] args) {
        Book book1 = new Book();
        Book book2 = Book.withTitle("Effective Java");
    }
}
```

Book이라는 생성자를 이용한 book1이라는 객체보다 팩토리 메서드를 이용하게 되면 코드를 좀 더 직관적으로 쉽게 파악이 가능하다.



생성자는 같은 타입의 파라미터를 받는 생성자를 두 개 이상 만들 수 없지만 팩토리 메소드를 이용하면 가능하다.

```java
class Book {
    private String title;
    private String author;
    
    public Book() {
    }

    public Book(String title) {
        this.title = title;
    }
    
    public Book(String author){
        this.author = author;
    }
}
```



이런 식의 생성자를 사용하게 되면 같은 시그니처를 사용하기에 생성자 사용이 불가능하다. 물론 다른 타입의 파라미터로 이루어진 경우 아래와 같이 사용은 가능하지만 이는 좋지 못한 방식이다.

```java
class Book {
    private String title;
    private String author;
    private int page;
    
    public Book() {
    }

    public Book(String title, int page) {
        this.title = title;
        this.page = page;
    }

    public Book(int page, String title) {
        this.title = title;
        this.page = page;
    }
}
```



이러한 경우 아래와 같이 코드를 작성하면 사용이 가능하다.

```java
class Book {
    private String title;
    private String author;

    public Book() {
    }

    public Book(String title) {
        this.title = title;
    }

    public static Book withTitle(String title) {
        return new Book(title);
    }

    public static Book withAuthor(String author) {
        Book book = new Book();
        book.author = author;
        return book;
    }

    public static void main(String[] args) {
        Book book1 = new Book();
        Book book2 = Book.withTitle("Effective Java");
        Book book3 = Book.withAuthor("Joshua Bloch");
    }
}
```



정적 팩토리 메소드가 무조건적으로 좋은 경우는 아니지만 한 클래스에 시그니처가 같은 생성자가 여러 개가 필요할 경우에는 유용하게 사용이 가능하다.



#### 2. 호출될 때 마다 인스턴스를 새로 생성하지 않아도 된다.

- 객체를 생성하지 않기 때문에 코스트가 높은 객체가 자주 요청되는 상황에서 성능을 끌어올릴 수 있다.

  - 플라이웨이트 패턴(Flyweight Pattern)과 비슷하다고 볼 수 있다.

    > 플라이웨이트 패턴(참고자료)
    >
    > https://ko.wikipedia.org/wiki/%ED%94%8C%EB%9D%BC%EC%9D%B4%EC%9B%A8%EC%9D%B4%ED%8A%B8_%ED%8C%A8%ED%84%B4

**인스턴스 통제** - 언제 어떤 인스턴스를 살아있게 할지 인스턴스의 생명주기를 통제

- 클래스를 싱글톤, 인스턴스화 불가로 만들 수 있다.
- 불변 값 클래스에서 동치인 인스턴스가 단 하나임을 보장한다.



#### 3. 반환 타입의 하위 타입 객체를 반환할 수 있다.

반환할 객체의 클래스를 자유롭게 선택할 수 있어 클래스의 유연성이 생겨, API를 만들 때 구현 클래스를 공개하지 않고 객체를 반환이 가능하다. 즉, API 사용을 위해 익혀야 하는 개념이 줄어들어 편의성을 제공하게 된다.

**자바 8 이전**에는 인터페이스에 정적 메소드를 선언할  수 없었다. 따라서 인터페이스를 반환하는 정적 메소드를 필요로 하게되면 인스턴스화 불가한 동반 클래스를 만들어서 그 안에 정의해야 했다.

하지만 자바 8버전부터는 인터페이스가 정적 메소드를 가질 수 없다는 제한이 풀려서 동반 클래스를 둘 필요가 없어졌다.

- 굳이 별도의 문서를 찾아가며 구현 클래스가 무엇인지 알아보지 않아도 된다.

  ※ **자바 8**에서 인터페이스는 public 정적 멤버만 허용하고, **자바 9**에서는private 정적메소드까지 허용한다

  - 정적 필드와 정적 멤버 플래스는 public이어야 한다.



#### 4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.

반환 타입의 하위 타입이기만 하면 어떤 클래스의 객체를 반환하든 상관없다. 심지어 다른 클래스가 객체를 반환해도 된다.

책에 예시로 나온 EnumSet 클래스 같은 경우 클라이언트는 내부에 두 클래스의 존재를 모르지만 원소의 수에 따라 원소가 64개 이하이면 **RegularEnumSet**을 반환하고, 64개를 초과하면 **JumboEnumSet** 을 반환한다.

```java
public static <E extends Enum<E>> EnumSet<E> noneOf(Class<E> elementType) {
        Enum<?>[] universe = getUniverse(elementType);
        if (universe == null)
            throw new ClassCastException(elementType + " not an enum");
        if (universe.length <= 64)
            return new RegularEnumSet<>(elementType, universe);
        else
            return new JumboEnumSet<>(elementType, universe);
}
```



#### 5. 정적 팩토리 메서드를 작성하는 시점에 반환할 객체의 클래스가 존재하지 않아도 된다.

이러한 유연함 덕에 서비스 제공자 프레임워크를 만들 수 있다.

서비스 제공자 프레임워크의 구성요소

- 3개의 핵심 컴포넌트
  - `서비스 인터페이스` : 구현체의 동작을 정의
  - `서비스 등록 API` : 제공자가 구현체를 등록할 때 사용
  - `서비스 접근 API` : 클라이언트가 서비스의 인스턴스를 얻을 때 사용
- 종종 사용되는 네 번째 컴포넌트
  - `서비스 제공자 인터페이스` : 서비스 인터페이스의 인스턴스를 생성하는 팩터리 객체



클라이언트는 **서비스 접근 API**를 이용해서 원하는 구현체를 가져올 수 있다.

- 조건을 명시하지 않을 경우 기본 구현체 혹은 지원하는 구현체들을 돌아가며 반환한다.이러한 서비스 접근 API가 서비스 제공자 프레임워크의 근간인 유여한 정적 팩토리 메소드의 실체다. 



서비스 제공자 인터페이스가 없다면 리플렉션을 이용해서 구현체를 인스턴스화 역할을 수행하도록 한다.

- 예시 : JDBC

  - ```java
    Class.forName("oracle.jdbc.driver.OracleDriver"); 
    Connection conn = null; 
    conn = DriverManager.getConnection("jdbc:oracle:thin:@localhost:1521:ORA92", "scott", "tiger"); 
    ```

  - `DriverManager.registerDriver` : 제공자 등록 API 역할(oracle, mysql ...)

  - `Connection ` :  서비스 인터페이스 역할

  - `DriverManager.getConnection ` : 서비스 접근 API 역할

  - `Driver` : 서비스 제공자 인터페이스 역할

  

------



### 정적 팩토리 메소드의 단점



#### 1. 상속하기 위해서 public이나 protected 생성자가 필요하기 때문에 정적 팩토리 메소드만 제공하면  하위 클래스를 만들 수 없다.

책에서 언급한 java.util.Collections 로 만든 구현체는 상속할 수 없다.



#### 2. 프로그래머가 찾기 어렵다.

생성자처럼 JavaDoc에서 따로 모아주는 기능이 존재하지 않기 때문에 API 문서를 잘 정리하지 않으면 개발자가 의미를 정확히 알기 어렵다.

따라서 널리 알려진 규약에 따라 짓는 방식으로 이러한 문제점을 보완해야 한다.

아래는 책에서 알려준 흔한 명명방식이다.

- `from `: 매개변수 하나로 하나의 인스턴스 만듬
- `of `: 여러 매개변수로 인스턴스 만듬
- `valueOf `: from 과 of의 자세한 버전
- `getInstance`, `instance `: 인스턴스를 반환(매개변수가 없는 경우 같은 인스턴스임을 보장하지 않음)
- `create`, `newInstance` : 매번 새로운 인스턴스를 생성하여 반환(같은 인스턴스임을 보장)
- `getType ` , `newType`: 인스턴스를 생성할 때, 해당 클래스가 아닌 다른 클래스에서 생성할 때 사용
- `Type `: `getType`, `newType `의 간략한 버전

------



### 정적 팩토리 메소드의 단점



#### 1. 상속하기 위해서 public이나 protected 생성자가 필요하기 때문에 정적 팩토리 메소드만 제공하면  하위 클래스를 만들 수 없다.

책에서 언급한 java.util.Collections 로 만든 구현체는 상속할 수 없다.



#### 2. 프로그래머가 찾기 어렵다.

생성자처럼 JavaDoc에서 따로 모아주는 기능이 존재하지 않기 때문에 API 문서를 잘 정리하지 않으면 개발자가 의미를 정확히 알기 어렵다.

따라서 널리 알려진 규약에 따라 짓는 방식으로 이러한 문제점을 보완해야 한다.

아래는 책에서 알려준 흔한 명명방식이다.

- `from `: 매개변수 하나로 하나의 인스턴스 만듬
- `of `: 여러 매개변수로 인스턴스 만듬
- `valueOf `: from 과 of의 자세한 버전
- `getInstance`, `instance `: 인스턴스를 반환(매개변수가 없는 경우 같은 인스턴스임을 보장하지 않음)
- `create`, `newInstance` : 매번 새로운 인스턴스를 생성하여 반환(같은 인스턴스임을 보장)
- `getType ` , `newType`: 인스턴스를 생성할 때, 해당 클래스가 아닌 다른 클래스에서 생성할 때 사용
- `Type `: `getType`, `newType `의 간략한 버전

------





## Item2. 생성자에 매개변수가 많다면 빌더를 고려하라

매개변수가 많은 클래스가 있을 경우의 3가지 코드를 예시로 들어보겠다.



#### 점층적 생성자 패턴

`점층적 생성자 패턴`은 가장 단순한 패턴으로 필수로 들어가야하는 파라미터를 포함한 생성자와 선택적으로 필요한 파라미터를 포함하는 생성자를 각각 만들어주는 것이다.

```java
public class item2 {

    private int required1;
    private int required2;
    private int required3;
    private int optional1;
    private int optional2;

    public item2(int required1, int required2, int required3) {
        this.required1 = required1;
        this.required2 = required2;
        this.required3 = required3;
    }

    public item2(int required1, int required2, int optional1, int required3) {
        this.required1 = required1;
        this.required2 = required2;
        this.optional1 = optional1;
        this.required3 = required3;
    }

    public item2(int required1, int required2, int optional1, int optional2, int required3) {
        this.required1 = required1;
        this.required2 = required2;
        this.optional1 = optional1;
        this.optional2 = optional2;
        this.required3 = required3;
    }
}
```

점층적 생성자 패턴은 쓰기 가장 간단하지만 **매개변수가 많아지면 코드 작성 및 가독성이 안좋아진다.**

- 각각의 매개변수가 무엇을 뜻하는지, 매개변수를 몇 개 사용했고, 몇 번째 입력중인지 주의깊게 살펴보아야 한다.



#### 자바빈즈 패턴

매개변수가 없는 생성자로 객체를 만들고, `setter`를 이용해서 원하는 매개변수의 값을 설정하는 방식이다.

```java
public class item2 {

    private int required1;
    private int required2;
    private int required3;
    private int optional1;
    private int optional2;

    public item2() {}

    public void setRequired1(int val){
        required1 = val;
    }

    public void setRequired2(int val){
        required2 = val;
    }

    public void setRequired3(int val){
        required3 = val;
    }

    public void setOptional1(int val){
        optional1 = val;
    }

    public void setOptional2(int val){
        optional2 = val;
    }
}

```

코드를 확인해보면 점층적 생성자 패턴의 단점이 사라지고, 가독성 좋은 코드가 생성되었다.



##### 자바빈즈 패턴의 단점

객체 하나를 만들기 위해서 여러 개의 메소드를 호출해야 하고, 객체가 완전히 생성되기 전에는 일관성이 무너진다.

- 생성자를 통한 유효성 검사가 불가능 함.

클래스를 불변으로 만들 수 없다.

※ **불변(immutable)** : 어떠한 변경도 허용하지 않는다는 뜻, 주로 변경을 허용하는 가변 객체와 구분하는 용도



#### 빌더 패턴

필요한 객체를 직접 만드는 대신 **필수 매개변수만으로 생성자를 호출**해 빌더 객체를 얻고, **setter를 이용하여 선택 매개변수를 설정**한다. 그 이후 매개변수가 없는 build 메소드를 호출해 필요한 객체를 얻는다.(보통은 일관성을 위한 불변 객체)

```java
public class Item2 {

    private final int required1;
    private final int required2;
    private final int required3;
    private final int optional1;
    private final int optional2;

    public static class Builder {
        //필수 매개변수
        private final int required1;
        private final int required2;
        private final int required3;

        //옵션 매개변수
        private int optional1 = 0;
        private int optional2 = 0;

        public Builder(int required1, int required2, int required3) {
            this.required1 = required1;
            this.required2 = required2;
            this.required3 = required3;
        }

        public Builder optional1(int val) {
            optional1 = val;
            return this;
        }

        public Builder optional2(int val) {
            optional2 = val;
            return this;
        }
        public Item2 build(){
            return new Item2(this);    
        }
    }

        private Item2(Builder builder) {
            required1 = builder.required1;
            required2 = builder.required2;
            required3 = builder.required3;
            optional1 = builder.optional1;
            optional2 = builder.optional2;
        }
}
```

Item2 라는 클래스는 분변이고, 필수 및 선택적 매개변수들을 한 곳에 모아두었기 때문에 setter를 통해서 필요한 매개변수를 호출할 수 있다. 이러한 방식을 `fluent API` 또는 `method chaining`이라고 한다.

- 사용 예시

  - ```java
    Item2 item = new Builder(1, 2, 3)
        .optional1(1)
        .optional2(2)
        .build();
    ```

빌더 패턴은 **쉽고**, **가독성이 좋다.** 이는 `명명된 선택적 매개변수(named optional parameters)`를 모사한 것이다.



빌더 패턴은 계층적으로 설계된 클래스와 쓰기 좋은데 추상클래스는 추상 빌더를, 구현 클래스는 구현 빌더를 가지도록 하여 이를 상속하는 클래스들에서 다양한 계층구조의 빌더 패턴을 확인할 수 있다.



책의 예시 코드를 보면 Pizza, NyPizza, Calzone 코드로 예시를 설명하고 있다.

- pizza.java

  - ```java
    import java.util.EnumSet;
    import java.util.Objects;
    import java.util.Set;
    
    public abstract class Pizza {
        public enum Topping {HAM, MUSHROOM, ONION, PEPPER, SAUSAGE}
    
        final Set<Topping> toppings;
    
        abstract static class Builder<T extends Builder<T>> {
            EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
    
            public T addTopping(Topping topping) {
                toppings.add(Objects.requireNonNull(topping));
                return self();
            }
    
            abstract Pizza build();
    
            protected abstract T self();
        }
    
        Pizza(Builder<?> builder) {
            toppings = builder.toppings.clone();
        }
    }
    ```

    여기서 추상 메소드인 **self()**를 사용하여 하위 클래스에서 따로 형변환 없이 메소드 연쇄를 지원한다.

    - 이러한 방식을 `시뮬레이트한 셀프타입(simulated self-type)` 이라고 한다.

- NyPizza.java

  - ```java
    public class NyPizza extends Pizza {
        public enum Size {SMALL, MEDIUM, LARGE}
    
        private final Size size;
    
        public static class Builder extends Pizza.Builder<Builder> {
            private final Size size;
    
            public Builder(Size size) {
                this.size = size;
            }
    
            @Override
            NyPizza build() {
                return new NyPizza(this);
            }
    
            @Override
            protected Builder self() {
                return this;
            }
        }
    
        private NyPizza(Builder builder) {
            super(builder);
            size = builder.size;
        }
    }
    ```

- Calzone.java

  - ```java
    public class Calzone extends Pizza {
        private final boolean sauceInside;
    
        public static class Builder extends Pizza.Builder<Builder> {
            private boolean sauceInside = false;
    
            public Builder sauceInside() {
                this.sauceInside = true;
                return this;
            }
    
            @Override
            Calzone build() {
                return new Calzone(this);
            }
    
            @Override
            protected Builder self() {
                return this;
            }
        }
    
        private Calzone(Builder builder) {
            super(builder);
            this.sauceInside = builder.sauceInside;
        }
    }
    ```

각각 하위 클래스인 `NyPizza.java`와 `Calzone.java`의 빌더가 정의한 build 메소드는 해당하는 구현 하위 클래스인 NyPizza와 Calzone을 반환한다.

이 케이스처럼 **하위 클래스의 메서드가 상위의 메서드가 정의한 반환 타입이 아니라 그 하위 타입을 반환하는 기능**을 `공변 반환 타이핑(covariant return typing)` 이라 한다. 이 기능을 사용하면 클라이언트가 형변환에 신경쓰지 않아도 된다.



##### 빌더 패턴의 단점

객체를 만들려면 빌더를 만들어야 하고, 이렇게 코드의 양이 증가하게 되면 성능이 민감한 상황에서 코스트가 커지는 상황이 발생할 수 있다.

또한 매개변수가 적을 경우에는 점층적 생성자 패턴이 더 효율적일 수 있다. 하지만 시스템을 장기적으로 운영/유지보수 하게되면 매개변수가 추가되는 경우가 많고, 이러한 경우에 점층적 생성자 패턴을 이용하여 빌드 패턴으로 전환하는 공수가 또 들기 때문에 애초에 빌더 패턴을 이용하는 편이 낫다.

------





## Item 3. private 생성자나 열거 타입으로 싱글톤임을 보증하라.

#### 싱글톤

- 인스턴스를 **오직 하나만** 생성할 수 있는 클래스
- 무상태 객체(stateless Object)
- 시스템 컴포넌트
- DB Connection Pool 등

클래스를 싱글턴으로 만들면 이를 사용하는 클라이언트를 테스트하기 어려워질 수 있다. 타입을 인터페이스로 정의한 다음 그 인터페이스를 구현해 만든 싱글턴이 아니라면 싱글턴 인스턴스를 가짜 구현(mock)으로 대체할 수 없기 때문이다.



### 싱글톤 생성 방식

#### 1. public static final을 이용한 방식

```java
public class Book {
		public static final Book INSTANCE = new Book();
		private Book(){}
		
		public void borrowBook(){}
}
```

public, protected 생성자가 없기 때문에 Book.INSTANCE를 초기화 할 때만 호출되어 인스턴스가 전체 시스템에서 하나 뿐임이 보장된다.

예외적으로 권한이 있는 클라이언트는 리플렉션 API인 `AccessibleObject.setAccessible`을 사용해 private 생성자를 호출할 수 있다. 이러한 공격을 방지하고자 한다면, 생성자에서 두 번 객체를 생성하려고 할 때 예외를 던지게 하면 된다.

**※ 리플렉션 API**

- 리플렉션은 런타임에 클래스의 런타임 동작을 검사하거나 수정하는 프로세스이다.

- 리플렉션 API는 IDE나 디버거, 테스트 툴 등에서 사용된다.

  > https://docs.oracle.com/javase/tutorial/reflect/
  >
  > https://www.javatpoint.com/java-reflection

또한 코드를 간결하게 볼 수 있다.



#### 2. 정적 팩토리 방식

```java
public class Book {
	private static final Book INSTANCE = new Book();
	private Book(){}
		
	public static Book getInstance(){
        return INSTANCE; 
    }
    
    public void borrowBook(){}
}
```

첫 번째 만든 방식과 같이 여기서도 getInstance에서 항상 같은 객체의 참조를 반환하기 때문에 리플렉션을 제외하고는 또 다른 Book이라는 인스턴스를 만들 수 없다.

정적 팩토리 방식의 장점은 API를 변경하지 않고도 getInstance를 변경하여 싱글톤이 아니게 변경이 가능하다.

또한 정적 팩토리를 `제네릭 싱글톤 팩토리`로 만들 수 있고, 정적 팩토리의 메소드 참조를 `공급자(Supplier<>)`로 사용할 수 있다.



싱글톤 클래스를 1번과 2번 방식으로 `직렬화`하기 위해서는 모든 인스턴스 필드를 `일시적(transient)`라고 선언하고 `readResolve`메소드를 제공해야 `역직렬화`시에 새로운 인스턴스가 만들어지는 것을 방지할 수 있다. 만약 이렇게 하지 않으면 초기화해둔 인스턴스가 아닌 다른 인스턴스가 반환된다.

**※ 직렬화(serializable)**

- 자바 시스템 내부에서 사용되는 **객체 또는 데이터를 외부의 자바 시스템에서도 사용할 수 있도록 바이트(byte) 형태로 데이터 변환하는 기술**(`직렬화`)과 **바이트로 변환된 데이터를 다시 객체로 변환하는 기술**(`역직렬화`)을 아우르는 말

  > https://techblog.woowahan.com/2550/
  >
  > https://techblog.woowahan.com/2551/

```java
private Object readResolve(){ 
		return INSTANCE; 
}
```



#### 3. 열거 타입 방식

간결하고 추가적인 노력 없이 직렬화가 가능하다. 또한 아주 복잡한 직렬화 상황이나 리플렉션 공격에 대해서도 다른 인스턴스가 생성되는 것을 막아준다.

대부분의 상황에서는 원소가 하나뿐인 열거타입이 싱글턴을 만드는 가장 좋은 방법이다. 단, 만들려는 싱글턴이 **Enum 외의 클래스를 상속해야 한다면 이 방법은 사용할 수 없다.**

```java
public enum Book{
    INSTANCE;
    
    public void borrowBook(){}
}
```

