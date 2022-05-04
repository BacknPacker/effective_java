# Effective Java 4장 제네릭



## Item 26. 로 타입은 사용하지 말라

#### 제네릭이란

* 자바 5부터 사용된 개념으로 클래스와 인터페이스 선언에 타입 매개변수가 쓰이면 제네릭 클래스, 제네릭 인터페이스라고 하고 이를 통틀어 제네릭 타입 이라고 한다.
* 제네릭 타입은 매개변수화 타입을 정의하는데 클래스 이름이 나오고 `<>` 안에 실제 타입 매개변수를 나열한다.
* 제네릭 타입을 정의하면 그에 딸린 로 타입도 정의된다.
  * 로 타입 : 제네릭 타입에서 타입 매개변수를 사용하지 않는 것. ex) List<E> 에서의 List



이전의 사용했던 컬렉션의 로 타입은 컴파일하자마자 오류가 발생하지 않기 때문에 오류가 발생하고 해결하기 위해서는 상당히 많은 코드들을 거슬러 올라가야 하지만 제네릭을 활용하면 컴파일 시에 오류가 발생했음을 알 수 있다.

```java
private final Collection stamp = ;
```

```java
private final Collection<Stamp> stamp = ;
```



자바에서는 로 타입을 쓰는 것을 막아놓지는 않았지만 사용하면 안된다. 사용하게 되면 제네릭이 안겨주는 안전성과 표현력을 모두 잃게된다.



#### 로 타입의 활용 방안

로 타입은 호환성 때문에 사용하고 있다. 제네릭은 자바가 나오면서 같이 나온 개념이 아니기 때문에 기존의 코드와의 호환성을 위해서는 로 타입도 동작을 해야만 했다. 이러한 마이그레이션 호환성을 위해서 로 타입을 지원하고 제네릭 구현에는 소거 방식을 사용하기로 했다. 

List같은 로 타입은 안되지만 List<Object>처럼 임의 객체 허용하는 타입은 괜찮다.

*  List<Object>는 컴파일러에게 모든 타입을 허용한다는 의사를 명확히 전달한 코드이기 때문에 List를 매개변수로 받는 메서드는 List<String>과 같은 List도 전달하는데 문제가 없지만, List<Object>를 매개변수로 받는 메서드에는 넘길 수 없다. 
  * 제네릭의 **하위 타입 규칙** 때문인데, List<String>은 List의 하위 타입이지만 List<Object>의 하위 타입은 아니기 때문이다.
  * List과 같은 로 타입을 매개변수로 사용하면 타입 안정성을 잃게 된다.



```java
public static void main(String[] args) {
    List<String> strings = new ArrayList<>();
    unsafeAdd(strings, Integer.valueOf(42));
    String s = strings.get(0);
    
}

public static void unsafeAdd(List list, Object o) {
    list.add(o);
}
```

이 코드를 실행시키면 strings.get(0)의 결과를 형변환시에 `ClassCastException`이 발생한다. 이는 strings.get(0)이 수행 될 때 컴파일러는 자동으로 타입 매개변수로 선언된 String으로 형변환을 시도하게 되고, 42라는 값은Integer 타입이기에 형변환 예외가 발생하는 것이다.

같은 코드를 로 타입으로 사용하게 되면 아래와 같다.

```java
public static void main(String[] args) {
    List<String> strings = new ArrayList<>();
    unsafeAdd(strings, Integer.valueOf(42));
    String s = strings.get(0);
    
}

public static void unsafeAdd(List<Object> list, Object o) {
    list.add(o);
}
```

이 코드는 바로 컴파일 에러를 발생하며 실행조차 안된다. 이러한 의미로 로 타입을 사용하고 싶은 생각이 들긴 하지만 안전하지 않기 때문에 책에서는 `비한정적 와일드카드 타입`의 사용을 권장한다.

`비한정적 와일드카드` 타입은 제네릭 타입을 쓰고는 싶지만 실제 타입 매개변수가 무엇인지 모르거나 알고싶지 않을 때, 로 타입대신 **물음표를 사용하여 어떤 타입도 담을 수 있는 범용적인 매개변수화 타입이다.**

```java
static int numElementsInCommon(Set<?> s1, Set<?> s2){}
```

로 타입과의 차이점을 이야기하면 간단하게는 안전성의 차이이다. 로 타입의 경우 아무 원소나 넣을 수 있어 안전하지 않지만 와일드카드를 사용하면 어떤 원소도 넣을 수 없기 때문에 컴파일러가 제 역할을 하도록 한다.



#### 로 타입 사용의 예외

##### class 리터럴

클래스 리터럴에는 배열과 기본 타입을 제외하곤 매개변수화 타입을 사용하지 못하게 한다.



##### instanceof 연산자

런타임에는 제네릭 타입 정보가 지워지기 때문에 instanceof 연산자는 와일드카드 타입 이외의 매개변수화 타입에는 적용이 불가능하다. 따라서 아무 역할을 못하는 와일드 카드 타입을 사용하는 것 보다는 로 타입을 사용하는게 깔끔하다.



###### ※ 4장 전반적으로 많이 사용하는 용어

| 한글 명칭                | 영어 명칭               | 예시                               |
| ------------------------ | ----------------------- | ---------------------------------- |
| 매개변수화 타입          | parameterized type      | `List<String>`                     |
| 실제 타입 매개변수       | actual type parameter   | `String`                           |
| 제네릭 타입              | generic type            | `List<E>`                          |
| 정규 타입 매개변수       | formal type parameter   | `E`                                |
| 비한정적 와일드카드 타입 | unbounded wildcard type | `List<?>`                          |
| 로 타입                  | raw type                | `List`                             |
| 한정적 타입 매개변수     | bounded type parameter  | `<E extends Number>`               |
| 재귀적 타입 한정         | recursive type bound    | `<T extends Comparable<T>>`        |
| 한정적 와일드카드 타입   | bounded wildcard type   | `List<? extends Number>`           |
| 제네릭 메서드            | generic method          | `static <E> List<E> asList(E[] a)` |
| 타입 토큰                | type token              | String.class                       |

