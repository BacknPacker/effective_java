# Effective Java 4장 제네릭

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
| 제네릭 메소드            | generic method          | `static <E> List<E> asList(E[] a)` |
| 타입 토큰                | type token              | `String.class`                     |



## Item 26. 로 타입은 사용하지 말라

#### 제네릭이란

* 자바 5부터 사용된 개념으로 **클래스와 인터페이스 선언에 타입 매개변수가 쓰이면** 제네릭 클래스, 제네릭 인터페이스라고 하고 이를 통틀어 제네릭 타입 이라고 한다.
* 제네릭 타입은 매개변수화 타입을 정의하는데 클래스 이름이 나오고 `<>` 안에 실제 타입 매개변수를 나열한다.
* 제네릭 타입을 정의하면 그에 딸린 로 타입도 정의된다.
  * 로 타입 : 제네릭 타입에서 타입 매개변수를 사용하지 않는 것. ex) `List<E>` 에서의 List



이전의 사용했던 컬렉션의 로 타입은 컴파일하자마자 오류가 발생하지 않기 때문에 오류가 발생하고 해결하기 위해서는 상당히 많은 코드들을 거슬러 올라가야 하지만 제네릭을 활용하면 컴파일 시에 오류가 발생했음을 알 수 있다.

```java
private final Collection stamp = ...;
```

```java
private final Collection<Stamp> stamp = ...;
```



자바에서는 로 타입을 쓰는 것을 막아놓지는 않았지만 사용하면 안된다. 사용하게 되면 제네릭이 안겨주는 안전성과 표현력을 모두 잃게된다.



#### 로 타입의 활용 방안

로 타입은 호환성 때문에 사용하고 있다. 제네릭은 자바가 나오면서 같이 나온 개념이 아니기 때문에 기존의 코드와의 호환성을 위해서는 로 타입도 동작을 해야만 했다. 이러한 마이그레이션 호환성을 위해서 로 타입을 지원하고 제네릭 구현에는 소거 방식을 사용하기로 했다. 

List같은 로 타입은 안되지만 List<Object>처럼 임의 객체 허용하는 타입은 괜찮다.

*  List<Object>는 컴파일러에게 모든 타입을 허용한다는 의사를 명확히 전달한 코드이기 때문에 List를 매개변수로 받는 메소드는 List<String>과 같은 List도 전달하는데 문제가 없지만, List<Object>를 매개변수로 받는 메소드에는 넘길 수 없다. 
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



---



## Item 27. 비검사 경고를 제거하라

#### 제네릭을 사용하면서 주로 나오는 경고

* 비검사 형변환 경고
* 비검사 메소드 호출 경고
* 비검사 매개변수화 가변인수 타입 경고
* 비검사 변환경고



대부분의 비검사 경고는 IDE에서 `warning`으로 표기가 되며 컴파일러에서 지적을 해주기 때문에 쉽게 제거가 가능하고, 자바 7부터 지원하는 `<>`연산자를 이용하여 해결도 가능하다.



##### 가능한 모든 비검사 경고를 제거하라

* 제거하게 되면 코드는 타입 안전성이 보장되어 런타임에 `ClassCastException`이 발생하지 않는다.

##### 경고를 제거할 수 없지만 안전하다고 생각되면 @SuppressWarnings("unchecked")를 사용하라

* 경고 없이 컴파일은 되지만 만약에 문제가 있다면 런타임에는 여전히 `ClassCastException`를 던질 수 있다.

##### @SuppressWarnings 어노테이션은 항상 가능한한 좁은 범위로 적용하자.

##### @SuppressWarnings("unchecked")을 사용할 때 그 경고를 무시해도 되는 안전한 이유를 적어두자.



---



## Item 28. 배열보다는 리스트를 사용하라.



### 배열과 제네릭 타입의 차이점

#### 공변과 불공변

간단하게 말하면 공변은 함께 변한다는 뜻이다. 상위 클래스가 있고 이를 상속하는 하위 클래스가 아래와 같은 코드로 존재한다.

```
Super sub = new Sub();
```

공변은 아래처럼 배열에서도 함께 변해서 적용된다는 뜻입니다.

```
Super[] sub = new Sub[]{};
```

배열은 공변이기 때문에 상위 클래스가 변하게 되면 하위 클래스도 변하게 되지만 제네릭 타입은 그렇지 않다. 또한 배열은 실수를 런타임에서 발견하게 되지만 제네릭의 경우 컴파일 시 오류가 발생하기 때문에 디버깅에도 용이하다.

#### 실체화

배열은 실체화가 가능하다. 

배열은 런타임에도 자신이 담기로 한 원소의 타입을 인지하고 확인하기 때문에 잘못된 배열에 다른 종류의 배열(Long배열에 String 배열)을 넣으려 하면 `ArrayStoreException`이 발생한다.



제네릭은 타입 정보가 런타임에는 소거되어 원소 타입을 컴파일타임에만 검사하며, 런타임에는 알 수 조차 없다.

- 소거는 자바 5 이전 제네릭이 지원되기 전의 레거시 코드와 제네릭 타입을 함께 사용할 수 있게 해주는 메커니즘으로 자바 5가 제네릭으로 순조롭게 전환될 수 있도록 해준다.



이러한 두 가지 차이점으로 인해 제네릭과 배열은 어울리지 않는다. 또한 제네릭 배열은 **타입이 안전하지 않고**, 만약 허용되었을 경우 **컴파일러가 자동 생성한 형변환 코드에서 런타임에 `ClassCastException`가 발생할 수 있다.** 이는 런타임에 `ClassCastException`이 발생하는 일을 막아주겠다고 하는 제네릭 타입 시스템의 취지에 어긋난다.



#### 실체화 불가 타입

정규 타입 매개변수 E, 제네릭 타입 List<E>, 매개변수화 타입 릿 List<String>와 같은 값을 실체화 불가 타입이라 한다. 이러한 값들은 실체화가 되지 않아서 런타임에는 컴파일타임보다 타입 정보를 적게 가지는 타입이다.

* 소거 메커니즘으로 인해 매개변수화 타입 중에서 실체화가 가능한 타입은 List<?>나 Map<?,?>같은 비한정적 와일드카드 타입뿐이다.



또한 제네릭과 가변인수 메소드를 같이 사용하면 경고 메세지를 받게 되는데, 가변인수 메소드를 호출할 때마다 가변인수 매개변수를 담을 배열이 만들어진다. 이 때 이 배열의 원소가 실체화 불가 타입이라면 경고가 발생하게 된다.

* 이 경고는 `@SafeVarags`라는 어노테이션으로 대처할 수 있다.



---



## Item 29. 이왕이면 제네릭 타입으로 만들라



```java
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public Object pop() {
        if(size == 0) throw new EmptyStackException();
        Object result = elements[--size];
        elements[size] = null;
        return result;
    }
}
```

이 코드는 아이템 7에서 다룬 단순한 코드의 일부이다. 이 코드는 제네릭으로 바꾼다고 하면 시스템에는 아무 문제가 없고, 오히려 지금 코드가 제네릭을 활용하지 않아 불안전한 상태이기 때문에 스택에서 꺼낸 객체를 형변환할 때 런타임 오류가 날 가능성이 있다.

```java
public class Stack<E> {
    private E[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new E[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(E e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public E pop() {
        if(size == 0) throw new EmptyStackException();
        E result = elements[--size];
        elements[size] = null;
        return result;
    }
}
```

제네릭으로 변경은 했지만 전 장에서 말했듯이 `elements = new E[DEFAULT_INITIAL_CAPACITY];`라는 표현에서 우리가 사용하면 안된다고 했던 실체화 불가 타입으로 만들어진 배열이 존재한다. 이 문제는 두 가지 방법으로 해결이 가능하다.



#### 제네릭 배열 생성을 금지하는 제약을 우회하는 방법

기존의 코드를 `elements = (E[]) new Object[DEFAULT_INITIAL_CAPACITY];`로 Object 배열을 생성하고, 제네릭 배열로 형변환 하는 방식이다.

* 이렇게 하면 오류는 해결할 수 있지만, 대신 `Unchecked cast`경고가 생긴다.
  - 이 방식에서는 배열 elements는 private 필드에 저장되며, 클라이언트로 반환되거나 다른 메소드로 전달되는 일이 없습니다. 즉, 비검사 형변환은 안전하기 때문에  `@SuppressWarnings` 어노테이션을 통해 해당 경고를 숨기면 된다.

이 방식은 코드가 짧아서 가독성이 좋다는 장점과 형변환을 배열 생성 시에만 해주면 된다는 장점이 있다.

하지만 배열의 런타임 타입이 컴파일타임 타입과 달라 힙오염이 발생할 수 있다.



#### elements 필드 타입을 Object[]로 바꾸는 방법

```java
public class Stack<E> {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }
    ...
}
```

이런 식으로 코드를 변경하면 오류는 없지만 경고가 발생한다. 또한 pop 메소드에서 Object에서 E로 형변환이 되야 하기 때문에 런타임 오류가 발생할 수 있다.

* 경고는 `@SuppressWarnings("unchecked")` 어노테이션을 이용하고, 형변환 문제는 타입캐스팅을 해준다.

  ```java
  @SuppressWarnings("unchecked") E result = (E) elements[--size];
  ```

이 방식은 코드의 길이는 다소 길지만 힙오염이 발생하지 않는다는 장점이 있다.

- 타임에 ClassCastException이 발생하는 일을 막아주겠다는 제네릭 타입 시스템의 취지에 어긋난다.



---



##  Item 30. 이왕이면 제네릭 메소드로 만들라

메소드도 클래스와 마찬가지로 제네릭으로 만들 수 있다.

```java
public static Set union(Set s1, Set s2) {
		Set result = new HashSet(s1);
		result.addAll(s2);
		return result;
}
```

책에 나와있는 이 예시는 반환타입, 매개변수가 로 타입이기에 새로운 HashSet을 만들고 거기에 s1을 넣는 것이 안전하지 않고, addAll로 s2 를 넣는 것도 안전하지 않다. 이 메소드를 제네릭을 이용해서 안전하게 만들면 다음과 같다.

```java
public static <E> Set<E> union(Set<E> s1, Set<E> s2) {
    Set<E> result = new HashSet<>(s1);
    result.addAll(s2);
    return result;
}
```



#### 제네릭 싱글톤 팩토리

불변 객체를 여러 타입으로 활용할 수 있게 만들어야 하는 경우가 종종 생기는데, 제네릭은 런타임시 타입 정보가 소거 된다. 그래서 하나의 객체를 어떤 타입으로든 매개변수화가 가능한데, 이게 가능하려면 요청한 타입 매개변수에 맞게 객체의 타입을 바꿔주는 정적 팩토리를 만들어야 한다.

이러한 패턴을 **제네릭 싱글톤 팩토리**라 하며 `Collections.reverseOrder`같은 함수 객체나 `Collections.emptySet`같은 컬렉션용으로 사용한다.

책에서는 항등함수를 이용하여 예시를 보여주었다.

- 항등 함수 객체는 상태가 없고, 제네릭이 실체화된다면 항등함수를 타입별로 만들어야 하지만, 소거가 되기 때문에 제네릭 싱글턴 하나면 충분하다.

```java
public static UnaryOperator<Object> IDENTITY_FN = (t) -> t;

@SuppressWarnings("unchecked")
public static <T> UnaryOperator<T> identiyFunction() {
	return (UnaryOperator<T>) IDENTITY_FN;
```



```java
public static void main(String[] args) {
    String[] strings = { "삼베", "대마", "나일론" };
    UnaryOperator<String> sameString = identityFunction();
    for (String s : strings)
        System.out.println(sameString.apply(s));

    Number[] numbers = { 1, 2.0, 3L };
    UnaryOperator<Number> sameNumber = identityFunction();
    for (Number n : numbers)
        System.out.println(sameNumber.apply(n));
}
```



#### 재귀적 타입 한정(recursive type bound)

자기 자신이 들어간 표현식을 사용해 타입 매개변수의 허용 범위를 한정하는 것으로 주로 Comparable 인터페이스와 함께 사용된다.

```java
public interface Comparable<T> {
	int compareTo(T o);
}
```

위는 comparable 인터페이스이다. 여기서 T는 구현한 타입이 비교할 수 있는 원소를 정의하는데 String이나 Integer 등이 필요할 때마다 `Comparable<String>` , `Comparable<Integer>`와 같이 구현한다. 이것을 재귀적 타입 한정을 이용하면 아래와 같이 사용할 수 있다.

```java
public static <E extends Comparable<E>> Optional<E> max(Collection<E> c);
```

타입한정인 `<E extends Comparable<E>>`은 **모든 타입 E는 자신과 비교할 수 있다.** 아래에 이를 구현한 메소드는 원소의 순서 기준으로 최댓값을 계산하고, 컴파일 오류가 발생하지 않는 코드이다.

```java
public static <E extends Comparable<E>> Optional<E> max(Collection<E> c) {
    if(c.isEmpty()){
    	return Optional.empty();  
    } 
    
    E result = null;
    for (E e : c) {
        if(result == null || e.compareTo(result) > 0) {
            result = Objects.requireNonNull(e);
        }
    }
    return Optional.of(result);
}
```



재귀적 타입 한정은 복잡해질 가능성도 존재하긴 하지만 그런 경우는 많지 않고, 반환값을 명시적으로 형변환해야 하는 메소드보다 제네릭 메소드가 훨씬 안전하고 사용하기도 쉽다.



---



## Item 31. 한정적 와일드카드를 사용해 API 유연성을 높이라



매개변수화 타입은 불공변이기 때문에 서로 다른 타입이 있을 때 상/하위가 나뉘지 않는다. 예를 들어 `List<String>`은 `List<Object>`의 하위 타입이 아니고, 그렇기에 `List<String>`은 `List<Object>`가 하는 일을 제대로 수행하지 못한다.

실재로 책에 나온 예시를 해보면 Number타입의 Integer값을 넣을 때도 제대로 동작해야 하지만 오류가 나오는 것을 볼 수 있다.

```java
public class Stack<E> {
    public Stack();
	public void push(E e);
    public E pop();
    public boolean isEmpty();
    public void pushAll(Iterable<E> src) {
        for(E e : src) push(e);
    }
}
```

```java
Stack<Number> numStack = new Stack<>();
Iterable<Integer> integers = ..;
numStack.pushAll(integers);
```

여기서 오류의 이유는 매개변수화 타입이 불공변이기 때문이다. 자바는 이러한 상황을 대처하기 위해 `한정적 와일드카드 타입`이라는 매개변수화 타입을 지원한다.

```java
public void pushAll(Iterable<? extends E> src){
    for(E e:src)
        push(e);
}
```

위 코드가 한정적 와일드카드 타입을 적용한 코드이다. 매개변수의 타입을 `Iterable<E>` 에서 `Iterable<? extends E>` 으로 와일드카드 타입을 적용해준 뒤 코드의 타입 안전하여 오류가 발생하지 않는 것을 확인할 수 있다.

이처럼 유연성을 극대화 하기 위해서는 원소의 생산자나 소비자용 입력 매개변수에 **와일드카드 타입**을 사용해야 한다.

어떤 타입의 와일드 카드 타입을 써야 하는지 아래처럼 생산자면 `extends`를, 소비자면 `super`를 사용하면 된다.

* PECS : producer - extends, consumer - super

여기서 말하는 생산자와 소비자의 정이는 스택 클래스에서 push와 pop이라고 생각하면 된다.

##### 생성자

* 입력 매개변수로부터 이 컬렉션으로 원소를 옮겨 담는다는 뜻

- ```
  public void pushAll(Iterable<? extends E> src) {
  	for (E e : src) {
  		push(e);
  	}
  }
  ```

##### 소비자

* 컬렉션 인스턴스의 원소를 입력 매개변수로 옮겨 담는다는 뜻

- ```
  public void popAll(Collection<? super E> dst) {
  	while (!isEmpty()) {
  		dst.add(pop());
  	}
  }
  ```

만약 API를 사용할 때 한정적 와일드카드 타입을 사용하면 클라이언트 코드에서도 와일드 카드 타입을 신경써야 하기 때문에 API에 문제가 있는 것이다.

또한 Collection에서 사용하는 코드들 중에서도 한정적 와일드카드 타입을 사용하는 것이 좋다. Comparable와 comparator은 일반적으로 소비자이므로 `Comparable<E>`보다는 `Comparable<? super E>`로 사용하는 편이 좋고, Comparator는 `Comparator<E>`보다 `Comparator<? super E>`를 사용하는 편이 좋다.



#### 타입 매개변수와 와일드카드

```java
public static <E> void swap(List<E> list, int i, int j);
public static void swap(List<?> list, int i, int j);
```

이 두 코드는 타입매개변수와 와일드카드를 사용한 코드이다. 여기서 두 코드의 장점이 각각 다르다. 일반적으로 public API로 사용할 때는 두 번째 코드가 좋다. 그 이유는 기본 규칙을 생각해보면 된다.

* 매소드 선언에 타입 매개변수가 한번만 나오면 와일드 카드로 대체하라.

  * 만약 비한정적 타입 매개변수면 비한정적 와일드카드로 변경하고, 한정적 타입 매개변수면 한정적 와일드카드로 변경한다.

    ###### 한정적 와일드카드

    *  `<T extends Number>`와 같이 특정 타입을 제한한다는 것

    ###### 비한정적 와일드카드

    * `?` 와 같은 타입으로 어떤 타입이 오던 관계가 없는 것

하지만 두 번째 코드는 사용하면 null이외에는 어떠한 값도 넣을 수 없기 때문에 `도우미 메소드`를 선언해야 한다.

```java
public static <E> void swapHelper(List<E> list, int i, int j) {
    list.set(i, list.set(j, list.get(i))); 
}
```

구현 자체는 다소 복잡하지만 와일드카드 기반의 코드를 선언할 수 있게 된다.



---



## Item 32. 제네릭과 가변인수를 함께 쓸 때는 신중하라



가변인수 메소드와 제네릭은 자바 5 때 함께 추가되었지만 잘 어울리지 않는다. 가변인수 메소드를 호출하면 가변인수를 담기 위한 배열이 자동으로 하나 만들어지고, 내부로 감춰야할 이 배열이 클라이언트에게 노출이 된다. 이 때 배열 안에 제네릭이나 매개변수화 타입이 포함되면 컴파일 에러가 발생한다.

대부분의 제네릭과 매개변수화 타입은 실체화 불가 타입이기에 생기는 에러로 메소드를 선언할 때 실체화 불가 타입으로 가변인수 매개변수를 선언할 경우 컴파일러가 경고를 내보내는데,  경고 내용은 매개변수화 타입의 변수가 타입이 다른 객체를 참조하면 **힙 오염**이 발생한다는 것이다.

```java
static void dangerous(List<String>... stringLists) {
    List<Integer> intList = List.of(42);

    Object[] objects = stringLists;
    objects[0] = intList; // 힙 오염 발생
    String s = stringLists[0].get(0); // ClassCastException
}
```

이 코드에서 dangerous 메소드에서 파라미터인 `List<String>`는 실체화 불가 타입을 가변인수로 사용했다. 선언 이후 내부에서 **명시적으로 형변환을 하지 않았고**, stringList의 원소는 **매개변수화 타입**이기 때문에 컴파일시 **암묵적으로 형변환**하게 된다. 이러한 이유로 타입 안정성이 깨지기 때문에 **제네릭 가변인수 배열 매개본수에 값을 저장하는 것은 안전하지 않다** 라고 할 수 있다.

제네릭 가변인수 매개변수를 받는 것이 안전하지 않으면서도 허용되는 이유는 **실무에서 매우 유용하기 때문이다.** 

* 자바 라이브러리에서 `Arrays.asList`, `Collections.addAll`, `EnumSet.of` 와 같은 것들이 있다.
  * 이 메소드들은 안전하다



### @SafeVarargs

자바 7 이후에는 우리가 `@SuppressWarnings("unchecked")`로 경고를 숨기듯이 `@SafeVarargs` 어노테이션을 통해서 **메소드 작성자가 그 메소드가 타입 안전함을 보장하는 장치로 사용**된다. 메소드가 **가변인수 배열에 아무것도 저장하지 않고 배열의 참조가 외부로 노출되지 않는다**면 안전하다고 한다. 가변인수 매개변수 배열이 오로지 메소드로 인수들을 전달하는 일만 수행한다면 안전함이 보장된다.



#### 주의사항

```java
static <T> T[] toArray(T... args){
		return args;
}
```

이러한 식으로 가변인수 매개변수 배열에 아무 값도 저장하지 않는 경우에는 타입 안전성이 깨질 수 있다. 컴파일시간에 컴파일러에 충분한 정보가 주어지지 않아 매개변수 배열을 그대로 반환해 힙 오염이 메소드 생성한 부분까지 전이될 수 있다.

```java
static <T> T[] pickTwo(T a, T b, T c) {
    switch (ThreadLocalRandom.current().nextInt(3)) {
        case 0: return toArray(a, b);
        case 1: return toArray(a, c);
        case 2: return toArray(b, c);
    }
    throw new AssertionError();
}
```

이러한 방식으로 랜덤하게 2개를 뽑는 메소드를 구현했다고 하면 컴파일상에서는 오류가 발생하지 않는다. 하지만 이를 사용하는 경우 문제가 생긴다.

```java
public static void main(String[] args) {
    String[] strings = pickTwo("좋은", "빠른", "저렴한");
}
```

이러한 식으로 사용하게 될 경우 `ClassCastException`을 발생시킨다. pickTwo 라는 메소드는 T 인스턴스 2개를 담은 매개변수 배열을 만들어 반환하는데 메소드의 결과는 항상 Object[] 일 수밖에 없다. 여기서 String[]으로 형변환하는 경우 컴파일러에서 자동 형변환을 하기 때문에 Object[]는 String[]의 하위 타입이 아니므로 형변환에 실패하면서 에러가 발생한다.



#### 예외 케이스

* `@SafeVarargs `어노테이션이 붙어있는 안전한 메소드
* 가변인수 매개변수 배열을 넘기지 않아도 되는 일반 함수



#### @SafeVarargs 어노테이션 사용 규칙

* 제네릭이나 매개변수화 타입의 가변인수 매개변수를 받는 모든 메소드에 @SafeVarargs를 붙이면 된다.



---



## Item 33. 타입 안전 이종 컨테이너를 고려하라

기존에 사용하던 제네릭 사용 방식은 컬렉션(`Set<E>`, `Map<K, V>`)이나 컨테이너(`ThreadLocal<T>`, `AtomicReference<T>`) 보다 유연하게 사용하기 위해 탄생한 것이 **타입 안전 이종 컨테이너 패턴**이다.

#### 타입 안전 이종 컨테이너 패턴(type safe heterogeneous container pattern)

* 컨테이너 대신 키를 매개변수화하여 컨테이너에 값을 넣거나 뺄 때 매개변수화한 키를 함께 제공하는 방식
* 제네릭 타입 시스템이 값의 타입이 키와 같음을 보장해줄 수 있게 된다. 



```java
public class Favorites {
		public <T> void putFavorite(Class<T> type, T instance);
		public <T> T getFavorite(Class<T> type);
}
```

타입 안전 이종 컨테이너의 예시이다. 각 타입의 Class 객체에서 class 의 클래스가 제네릭이기 때문에 Class 리터럴의 타입은 `Class`가 아닌 `Class<T>`이다. 

* ex) `String.class`의 타입은 `Class<String>`이고, `Integer.class`의 타입은 `Class<Integer>`이다.

컴파일타임 타입 정보와 런타임 타입 정보를 알아내기 위해 메서드들이 주고받는 class 리터럴을 **타입 토큰(type token)**이라 한다.

사용 예시는 아래와 같다.

```java
public static void main(String[] args){
		Favorites favorites = new Favorites();

    favorites.putFavorites(String.class, "Java");
    favorites.putFavorites(Integer.class, 1234);
    favorites.putFavorites(Class.class, Favorites.class);

    String favoriteStr = favorites.getFavorite(String.class);
    Integer favoriteInt = favorites.getFavorite(Integer.class);
    Class favoriteClass = favorites.getFavorite(Class.class);

    System.out.printf("%s %x %s %n", favoriteStr, favoriteInt, favoriteClass.getName());

}
```

```java
private static class Favorites {
    private Map<Class<?>, Object> favorites = new HashMap<>();

    public <T> void putFavorites(Class<T> type, T instance){
        favorites.put(Objects.requireNonNull(type), instance);
    }

    public <T> T getFavorite(Class<T> type) {
        return type.cast(favorites.get(type));
    }
}
```

Favorites 에서 favorites의 타입을 보면 `Map<Class<?>, Object>`이다. 이는 비한정적 와일드카드를 사용하여 맵이 아니라 키가 와일드카드 타입이므로 다양한 타입을 지원할 수 있도록 한다.

또한 맵의 값 타입이 Object라는 점은 키와 값 사이의 타입 관계를 보증하지 않아 모든 값이 키로 명시한 타입임을 보증하지 않는다. 자바 자체적으로 이 관계를 명시할 방법이 없지만 우리는 이 관계가 성립한다는 것을 알고있고, 이러한 이점을 누릴 수 있다.



#### 메소드 구현

##### putFavorite

putFavorite 메서드는 주어진 Class 객체와 즐겨찾기 인스턴스를 favorites 변수에 추가하면 끝난다. 이 때 키와 값 사이의 `타입 링크(type linkage)`정보는 버려지기 때문에 이 값이 해당 키 타입의 인스턴스라는 정보는 사라진다. 하지만 getFavorite 메서드에서 관계를 되살릴 수 있으니 문제없다.

##### getFavorite

getFavorite 메서드는 주어진 Class객체에 해당하는 값을 favorites 변수에서 꺼낸다. 이 값은 아직은 Object타입이지만 우리는 키로 해당 값의 올바른 타입을 알기 때문에 Class의 cast메서드를 사용해 이 객체 참조를 Class 객체가 가리키는 방향으로 동적 형변환한다. 

cast 메서드는 형변환 연산자의 동적 버전인데, 주어진 인수가 Class 객체가 알려주는 타입의 인스턴스인지 검사한 뒤 맞다면 인수를 반환하고 아니면 ClassCastException을 던진다. 그래서 이 코드가 깨끗하게 컴파일되면 getFavorites에서 호출하는 cast 메서드는 오류을 던지지 않아서 favorites 맵 안에 값은 해당 키의 타입과 항상 일치함을 알 수 있다.



#### 제약사항

* 악의적인 클라이언트가 Class 객체를 제네릭이 아닌 로타입으로 넘기면 인스턴스의 타입 안전성이 쉽게 깨진다.
  * 이 경우 문제가 되지만 클라이언트 코드에서 컴파일 시에 비검사 경고가 뜬다.
  * 타입 불변식을 어기지 않도록 하기 위해서는 instance의 타입이 type과 같은 타입인지 확인하여 타입 불변성을 지킬 수 있다.
* 실체화 불가 타입에는 사용이 불가능하다.
  * 이 제약에 대해서는 우회 방법이 존재하지 않는다.