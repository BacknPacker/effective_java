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

