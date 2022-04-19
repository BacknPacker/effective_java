# 🔥 [ Chapter3 ] 모든 객체의 공통 메서드
_<span style='font-weight: bold;'>`Object`</span>는 객체를 만들 수 잇는 구체 클래스지만 기본적으로는 상속해서 사용하도록 설계되었다.<br>_
_<span style='font-weight: bold;'>`Object`</span>에서 `final`이 아닌 메서드는 모두 재정의를 염두에 두고 설계된 것이라 재정의 시 지켜야 하는<br>_
_일반 규약이 명확히 정의되어 있다._

## 🎯  아이템 10. equals는 일반 규약을 지켜 재정의하라.
+ **`Object` 명세에 적힌 규약 ❗**
> 1️⃣ 반사성 : null이 아닌 모든 참조 값 x에 대해, x.equals(x)는 true다.<br>
> 2️⃣ 대칭성 : null이 아닌 모든 참조 값 x,y에 대해, x.equals(y)가 true면 y.equals(x)도 true다.<br>
> 3️⃣ 추이성 : null이 아닌 모든 참조 값 x,y,z에 대해, x.equals(y)가 true이고 y.equals(z)도 true면 x.equals(z)도 true다.<br>
> 4️⃣ 일관성 : null이 아닌 모든 참조 값 x,y에 대해, x.equals(y)를 반복해서 호출하면 항상 true를 반환하거나 항상 false를 반환한다.<br>
> 5️⃣ null-아님 : null이 아닌 모든 참조 값 x에 대해, x.equals(null)은 false다.<br>

 <br>

+ **잘못된 코드 - 대칭성 위배 ❗**
```java
public final class CaseInsensitiveString {
    private final String s;
    
    public CaseInsensitiveString(String s){
        this.s = Object.requireNonNull(s);
    }
    
    // 대칭성 위배 !!
    @Override
    public boolean equals(Object o){
        if(o instanceof CaseInsensitiveString) return s.equalsIgnoreCase(((CaseInsensitiveString) o).s);
        if(o instanceof String) return s.equalsIgnoreCase(((String) o));
        return false;
    }
    ... 
}
```
⚠️`CaseInsensitiveString`의 `equals`는 일반`String`을 알고있지만 `String`의 `equals`는 `CaseInsensitiveString`의 존재를 모른다는데 있다.<br>
⚠️ `equals` 규약을 어기면 그 객체를 사용하는 다른 객체들이 어떻게 반응할지 알 수 없다.<br>
⚠️ 구체 클래스를 확장해 새로운 값을 추가하면서 `equals` 규약을 만족시킬 방법은 존재하지 않는다.<br><br><br>

+ **양질의 `equals` 메서드 구현 방법을 단계별로 정리 ❗**
> 1️⃣ == 연산자를 사용해 입력이 자기 자신의 참조인지 확인한다.<br>
> 2️⃣ instanceof 연산자로 입력이 올바른 타입인지 확인한다.<br>
> 3️⃣ 입력을 올바른 타입으로 형변환한다.<br>
> 4️⃣ 입력 객체와 자기 자신의 대응되는 '핵심'필드들이 모두 일치하는지 하나씩 검사한다.<br>


## 🎯  아이템 11. equals를 재정의하려거든 hashCode도 재정의하라.
+ **`Object` 명세에 적힌 규약 ❗**
> 1️⃣ equals 비교에 사용되는 정보가 변경되지 않았다면, 애플리케이션이 실행되는 동안 그 객체의 hashCode 메서드는 <br>&nbsp; &nbsp; &nbsp; 몇 번을 호출해도 일관되게 항상 같은 값을 반환해야 한다.<br>
> 2️⃣ equals(Object)가 두 객체를 같다고 판단했다면, 두 객체의 hashCode는 똑같은 값을 반환해야 한다.<br>
> 3️⃣ equals(Object)가 두 객체를 다르다고 판단했더라도, 두 객체의 hashCode가 서로 다른 값을 반환할 필요는 없다.<br>

**⚠️`hashCode` 재정의를 잘못했을 때 크게 문제가 되는 조항은 두 번재다. 즉, 논리적으로 같은 객체는 같은 해시코드를 반환해야 한다.**


## 🎯  아이템 12. toString을 항상 재정의하라.
```java
@Override
public String toString() {
    return "User{" +
            "userId='" + userId + '\'' +
            ", userPw='" + userPw + '\'' +
            ", userNm='" + userNm + '\'' +
            '}';
}
```
+ `toString`은 그 객체가 가진 주요 정보 모두를 반환하는게 좋다.
+ `toString`이 반환한 값에 포함된 정보를 얻어올 수 있는 API를 제공하자.

## 🎯  아이템 13. clone 재정의는 주의해서 진행하라.
**`Cloneable`은 복제해도 되는 클래스임을 명시하는 용도의 <span style='color: red;'>믹스인 인터페이스</span>지만, 아쉽게도 의도한 목적을 제대로 이루지 못했다.**

**가장 큰 문제는 `clone` 메서드가 선언된 곳이 `Cloneable`이 아닌 `Object`이고, 그마저도 <span style='color: red;'>protected</span>라는 데 있다.**
```java
protected native Object clone() throws CloneNotSupportedException;
```
+ **`Object` 명세에 적힌 규약 ❗**
> 이 객체의 복사본을 생성해 반환한다. '복사'의 정확한 뜻은 그 객체를 구현한 클래스에 따라 다를 수 있다.<br>
> 일반적인 의도는 다음과 같다. 어떤 객체 x에 대해 다음 식은 참이다.<br><br>
> _x.clone() != x_<br><br>
> 또한 다음 식도 참이다.<br><br>
> _x.clone().getClass()_ == x.getClass()<br><br>
> 하지만 이상의 요구를 반드시 만족해야 하는 것은 아니다.<br>
> 한편 다음 식도 일반적으로 참이지만, 역시 필수는 아니다.<br><br>
> _x.clone().equals(x)_<br><br>
> 관례상, 이 메서드가 반환하는 객체는 super.clone을 호출해 얻어야 한다.<br>
> 이 클래스(Object를 제외한) 모든 상위 클래스가 이 관례를 따른다면 다음 식은 참이다.<br><br>
> _x.clone().getClass() == x.getClass()_<br><br>
> 관례상, 반환된 객체와 원본 객체는 독립적이어야 한다.<br>
> 이를 만족하려면 super.clone으로 얻은 객체의 필드 중 하나 이상을 반환 전에 수정해야 할 수도 있다.<br>


<br>

* 가변 상태를 참조하지 않는 클래스용 `clone` 메서드
```java
@Override
public Item13 clone() {
    try {
        return (Item13) super.clone();
    } catch (CloneNotSupportedException e) {
        throw new RuntimeException();
    }
}
```
이 메서드가 동작하게 하려면 Item13의 클래스 선언에 `Cloneable`을 구현해야한다.<br>
만약 구현하지 않는다면 `CloneNotSupportedException`을 던지게 된다.<br>
`clone` 메서드는 사실상 생성자와 같은 효과를 낸다.<br>
즉, `clone`은 원본 객체에 아무런 해를 끼치지 않는 동시에 복제된 객체의 불변식을 보장해야한다.<br>
따라서, 기본 원칙은 '복제 기능은 생성자와 팩터리를 이용하는게 최고'라는 것이다.<br>
단, 배열만은 `clone` 메서드 방식이 가장 깔끔한, 이 규칙의 합당한 예외라 할 수 있다.<br>
(배열은 각 elements도 재귀적으로 `clone`을 해야하기 때문이다.)


## 🎯  아이템 14. Comparable을 구혈할지 고려하라.
_**알파벳, 숫자, 연대 같이 순서가 명확한 값 클래스를 작성한다면 반드시 `Comparable` 인터페이스를 구현하자.**_
> 이 객체와 주어진 객체의 순서를 비교한다. 이 객체가 주어진 객체보다 작으면 음의 정수를,<br>
> 같으면 0을, 크면 양의 정수를 반환한다. 이 객체와 비교할 수 없는 타입의 객체가 주어지면<br>
> `ClassCastException` 을 던진다.

```java
public class Item14 implements Comparable<Item14> {

    private Integer integer;

    public Item14(Integer integer) {
        this.integer = integer;
    }

    @Override
    public int compareTo(Item14 o) {
        return Integer.compare(integer, o.integer);
    }

    public static void main(String[] args) {
        Item14 item = new Item14(3);
        Item14 item2 = new Item14(1);
        Item14 item3 = new Item14(5);
        Item14 item4 = new Item14(3);
        System.out.println(item.compareTo(item2)); // 1
        System.out.println(item.compareTo(item3)); // -1
        System.out.println(item.compareTo(item4)); // 0
    }
}
```
## ⭐ 결론
**_Object에서 final이 아닌 메서드는 모두 재정의를 염두에 두고 설계가 되었지만...<br>
반드시 각각의 규약 따라서 재정의 할 수 있도록한다._**