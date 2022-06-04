# 🔥 [ Chapter9 ] 일반적인 프로그래밍 원칙

## 🎯  아이템 57. 지역변수의 범위를 최소화하라.
지역변수의 유효 범위를 최소로 줄이면 코드 가독성과 유지보수성이 높아지고 오류 가능성은 낮아진다.

### ⭐ 지역변수의 범위를 줄이는 가장 강력한 기법 <br>

####1️⃣ 가장 처음 쓰일 때 선언하기<br>
```java
public class Main {
    public static void main(String[] args) {
        int scope = 0;

        /* main메서드가 완료될 때까지 scope변수는 살아있다. */
    }
}
```
변수를 항상 최상단에 선언하는 버릇을 가진 개발자가 존재한다. <br>
하지만 그건 변수의 범위를 메소드의 모든 범위에 속하게 하며 가독성마저 떨어지게 만드는 행위다.<br>
<br>

####2️⃣ 거의 모든 지역변수는 선언과 동시에 초기화해야 한다.<br>
초기화할 정보가 부족하다면 정보가 충분해질때까지 `선언`을 미루자. <br>
하지만 `try-catch`문의 경우 초기화시 예외가 발생할 경우는 블록 안에서 초기화 하자<br>
```java
/**
 * 코드 children 조회
 * @param supiCdId
 * @return
 */
public static List<CodeVO> getCodeChildren(String supiCdId) throws Exception {
    List<CodeVO> result = new ArrayList<>();
    CodeService codeService = null;
    try {
        codeService = (CodeService) ApplicationContextProvider.getContext().getBean("codeService");
        result = codeService.getCodeChildren(supiCdId);
        return result;
    } catch (Exception e){
        return result;
   }
}
```
<br>

####3️⃣ 메서드를 작게 유지하고 한 가지 기능에 집중하는 것이다.<br>

## 🎯  아이템 58. 전통적인 for 문보다는 for-each 문을 사용하라.
전통적인 `for`문과 비교했을 때 `for-each`문은 명료하고, 유연하고, 버그를 예방해준다.<br>
성능 저하도 없다. 가능한 모든 곳에서 `for`문이 아닌 `for-each`문을 사용하자.<br>

<br>

#### 하지만 안타깝게도 `for-each`문을 사용할 수 없는 상황이 세 가지 존재한다.<br>

1️⃣ **파괴적인 필터링** <br>
컬렉션을 순회하면서 선택된 원소를 제거해야 한다면 반복자의 remove 메서드를 호출해야한다. <br>
자바8부터는 Collection의 removeIf 메서드를 사용해 컬렉션을 명시적으로 순회하는 일을 피할 수 있다.<br>

<br>

2️⃣ **변형** <br>
리스트나 배열을 순회하면서 그 원소의 값 일부 혹은 전체를 교체해야 한다면 리스트의 반복자나<br>
배열의 인덱스를 사용해야한다.<br>

<br>

3️⃣ **병렬 반복** <br>
여러 컬렉션을 병렬로 순회해야 한다면 각각의 반복자와 인덱스 별수를 사용해 엄격하고 <br>
명시적으로 제어해야 한다.

## 🎯  아이템 59. 라이브러리를 익히고 사용하라.
#### **_“바퀴를 다시 발명하지 마라 (Don’t reinvent the wheel)”_** <br>
<br>
아주 특변한 나만의 기능이 아니라면 누군가 이미 라이브러리 형태로 구현해놓았을 가능성이 크다.<br>

<br>

* 표준 라이브러리를 사용하면 좋은 이점
1. 코드를 작성한 전문가의 지식과 여러분보다 앞서 사용한 다른 프로그래머들의 경험을 활용할 수 있다.
2. 핵심적인 일과 크게 관련 없는 문제를 해결하느라 시간을 허비하지 않아도 된다는 것이다.
3. 노력하지 않아도 성능이 지속해서 개선된다는 점이다.
4. 기능이 점점 더 많아진다.
5. 작성한 코드가 많은 사람에게 낯익은 코드가 된다.

<br>

## 🎯  아이템 60. 정확한 답이 필요하다면 float와 double은 피하라.
`float`와 `double` 타입은 과학과 공학 계산용으로 설계되었다. 이진 부동소수점 연산에 쓰이며,<br>
넓은 범위의 수를 빠르게 정밀한 '근사치'로 계산하도록 세심하게 설계되었다. 따라서, 정확한<br>
결과가 필요할 때는 사용하면 안 된다.<br>
`float`와 `double` 타입은 특히 금융 관련 계산과는 맞지 않는다.<br>
따라서, 금융 계산에는 `BigDecimal`, `int` 혹은 `long`을 사용해야 한다.<br>

## 🎯  아이템 61. 박싱된 기본 타입보다는 기본 타입을 사용하라.
### 기본 타입 vs 박싱된 기본 타입
1️⃣ 기본 타입은 값만 가지고 있으나, 박싱된 기본 타입은 값에 더해 식별성이란 속성을 갖는다.<br>
```java
public class Main {
    public static void main(String[] args) {
        int a = 1;
        int b = 1;
        System.out.println("(b == a) : " + (b == a)); // (b == a) : true
        Integer boxedIntegerA = new Integer(1);
        Integer boxedIntegerB = new Integer(1);
        System.out.println("(boxedIntegerB == boxedIntegerA) : " + (boxedIntegerB == boxedIntegerA)); // (boxedIntegerB == boxedIntegerA) : false
        System.out.println("boxedIntegerB.equals(boxedIntegerA) : " + boxedIntegerB.equals(boxedIntegerA)); // boxedIntegerB.equals(boxedIntegerA) : true
    }
}
```
<br>

2️⃣  기본 타입의 값은 언제나 유효하나, 박싱된 기본 타입은 유효하지 않은 값, 즉 null을 가질 수 있다.<br>
```java
public class Main {
    static int a;
    static Integer i;
    public static void main(String[] args) {
        if (a < 1) System.out.println(a);
        if (i < 1) System.out.println(i);
    }
}
```
<br>
<img width="40%" src="https://user-images.githubusercontent.com/55771326/171981822-83965121-0557-4805-929d-7331583f2c36.png" >

<br>

3️⃣  기본 타입이 박싱된 기본 타입보다 시간과 메모리 사용면에서 더 효율적이다. <br>

## 🎯  아이템 62. 다른 타입이 적절하다면 문자열 사용을 피하라.
1️⃣ 문자열은 다른 값 타입을 대신하기에 적합하지 않다. <br>
받는 데이터가 수치형이라면 int, float, BigInteger 등 적당한 수치 타입으로 변한해야한다.<br>
'예/아니오' 질문의 답이라면 적절한 열거 타입이나 boolean으로 변환해야 한다.<br>

2️⃣ 문자열은 열거 타입을 대신하기에 적합하지 않다. <br>

3️⃣ 문자열은 혼합 타입을 대신하기에 적합하지 않다. <br>
* 혼합 타입을 문자열로 처리한 부적절한 예 <br>
```java
String compoundKey = className + "#" + i.next();
```

4️⃣ 문자열은 권한을 표현하기에 적합하지 않다. <br>
```java
@Entity
public class Member {
    @Id
    private Long id;

    @Column(name = "name")
    private String username;

    private Integer age;

    @Enumerated(EnumType.ORDINAL)
    private RoleType roleType;
}
public enum RoleType {
    USER, ADMIN;
}
```
~~private String roleType~~ 은 권한에 적합하지 않다. <br>

<br>

## 🎯  아이템 63. 문자열 연결은 느리니 주의하라.
#### **_문자열 연결 연산자로 문자열 n개를 잇는 시간은 n^2에 비례한다._**
* 문자열 연결을 잘못 사용한 예 - 느리다!
```java
public String statement(){
    String result = "";
    for(int i = 0; i < numItems(); i++)
        result += lineForItem(i);
    return result;
}
```
품목이 많을 경우 이 메서드는 심각하게 느려질 수 있다. <br>
성능을 포기하고 싶지 않다면 `String` 대신 `StringBuilder`를 사용하자.

* StringBuilder를 사용하면 문자열 연결 성능이 크게 개선된다.
```java
public String statement2(){
    StringBuilder b = new StringBuilder(numItems() * LINE_WIDTH);
    for(int i = 0; i < numItems(); i++)
        b.append(lineForItem(i));
    return b.toString();
}
```

<br>

## 🎯  아이템 64. 객체는 인터페이스를 사용해 참조하라.
**적합한 인터페이스만 있다면 매개변수뿐 아니라 반환값, 변수, 필드를 전부 인터페이스 타입으로 선언하라.** <br>
```java
/* 할인 정책 인터페이스 */
public interface DiscountPolicy {
    int discount(Member member, int price);
}
/* 정액 할인 클래스 */
public class FixDiscountPolicy implements DiscountPolicy{
    
    private int discountFixAmount = 1000; // 1000원 할인
    
    @Override
    public int discount(Member member, int price) { ... }
}
/* 정률 할인 클래스 */
public class RateDiscountPolicy implements DiscountPolicy{

    private int discountPercent = 10;

    @Override
    public int discount(Member member, int price) { ... }
}
/* 주문 비지니스 로직 */
public class OrderServiceImpl implements OrderService{
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;
}
```
위 처럼 정액 할인에서 정률 할인으로 정책이 변경될 때 인터페이스로 선언하게되면 <br>
정률 할인 클래스를 주입해주면 정상 동작하게되어 훨씬 유연해질 수 있다.<br>

<br>

## 🎯  아이템 65. 리플렉션보다는 인터페이스를 사용하라.
리플렉션은 복잡한 특수 시스템을 개발할 때 필요한 강력한 기능이지만, 단점도 많다.<br>
컴파일타임에는 알 수 없는 클래스를 사용하는 프로그램을 작성한다면 리플렉션을 사용해야 할 것이다.<br>
단, 되도록 객체 생성에만 사용하고, 생성한 객체를 이용할 때는 적절한 인터페이스나 컴파일타임에 <br>
알 수 있는 상위 클래스로 형변환해 사용해야 한다.<br>
```java
@Before(VO_SETTING_EXPRESSION)
public void setVO(JoinPoint joinPoint) {
    Object[] objects = joinPoint.getArgs();
    if(SecurityContextHolder.getContext().getAuthentication()!= null) {
        InnoUser user = (InnoUser) SecurityContextHolder.getContext().getAuthentication().getPrincipal();
        AdminVO vo = user.getAdminVO();
        for (Object o : objects){
            Method[] methods = o.getClass().getMethods(); // 리플랙션
            Optional<Method> setRegDtime = Arrays.stream(methods).filter(e->e.getName().equals("setRegDate")).findFirst();
            Optional<Method> setModDtime = Arrays.stream(methods).filter(e->e.getName().equals("setModDate")).findFirst();
            // ... 생략
        }
    }
}
```

## 🎯  아이템 66. 네이티브 메서드는 신중히 사용하라.
네이티브 메서드를 사용하려거든 한번 더 생각하라. 네이티브 메서드가 성능을 개선해주는 일은 많지 않다. <br>
저수준 자원이나 네이티브 라이브러리를 사용해야만 해서 어쩔 수 없더라도 네이티브 코드는 최소한만 사용하고<br>
철저히 테스트하라. 네이티브 코드 안에 숨은 단 하나의 버그가 여러분 애플리케이션 전체를 훼손할 수도 있다.<br>

<br>

## 🎯  아이템 67. 최적화는 신중히 하라.
#### **_빠른 프로그램을 작성하려 안달하지 말자. 좋은 프로그램을 작성하다 보면 성능은 따라오게 마련이다._** <br>
1️⃣ 성능을 제한하는 설계를 피하라. <br>
완성 후 변경하기가 가장 어려운 설계 요소는 바로 컴포넌트끼리, 혹은 외부 시스템과의 소통 방식이다.<br>
이런 설계 요소들은 완성 후에는 변경하기 어렵거나 불가능할 수 있으며, 동시에 시스템 성능을 심각하게 제한할 수 있다.<br>

<br>

2️⃣ API를 설계할 때 성능에 주는 영향을 고려하라. <br>
public 타입을 가변으로 만들면, 즉 내부 데이터를 변경할 수 있게 만들면 불필요한 방어적 복사를 수없이 유발할 수 있다.<br>
인터페이스도 있는데 굳이 구현 타입을 사용하는 것 역시 좋지 않다. 특정 구현체에 종속되게 하여, 나중에 더 빠른 구현체가 <br>
나오더라도 이용하지 못하게 된다. <br>

<br>

3️⃣ 성능을 위해 API를 왜곡하는 건 매우 안 좋은 생각이다. <br>
신중하게 설계하여 깨끗하고 명확하고 멋진 구조를 갖춘 프로그램을 완성한 다음에야 최적화를 고려해볼 차례가 된다. <br>
물론 성능에 만족하지 못할 경우에 한정된다.<br>

## 🎯  아이템 68. 일반적으로 통용되는 명명 규칙을 따르라.
표준 명명 규칙을 체화하여 자연스럽게 베어 나오도록 하자. <br>
철자 규칙은 직관적이라 모호한 부분이 적은 데 반해, 문법 규칙은 더 복잡하고 느슨하다. <br>
자바 언어 면세의 말을 인용하자면 "오랫동안 따라온 규칙과 충동한다면 그 규칙을 맹종해서는 안 된다." <br>

| 식별자 타입     | 예                                                |
|------------|--------------------------------------------------|
| 패키지와 모듈    | org.junit.jupiter.api, com.google.common.collect |
| 클래스와 인터페이스 | Stream, FutureTask, LinkedHashMap, HttpClient    |
| 메서드와 필드    | remove, groupingBy, getCrc                       |
| 상수 필드      | MIN_VALUE, NEGATIVE_INFINITY                     |
| 지역변수       | i, denom, houseNum                               |
| 타입 매개변수    | T, E, K, V, X, R, U, V, T1, T2                   |


<br>

> Joshua Bloch, 『Effective Java 3/E』, 개앞맵시 옮김, 프로그래밍인사이트(2018), p343-384.