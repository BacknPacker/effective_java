# Effective Java 3장 모든 객체의 공통 메소드



## Item 10. equals는 일반 규약을 지켜 재정의하라

equals 메소드는 일부 다른 개체가 이 개체와 "동일한"지 여부를 나타내는 메소드이다.

> https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html

equals 메소드는 재정의하기 쉬워보이지만 종종 발생하는 문제점이 있다.



### equals를 재정의하면 안되는 경우

#### 각 인스턴스가 본질적으로 고유할 경우

* `Thread`와 같이 값을 표현하는 게 아닌 동작하는 개체를 표현하는 클래스일 경우



#### 인스턴스의 논리적 동치성을 검사할 일이 없을 경우

논리적 동치(Logical Equivalence) : 두 문장이나 수식이 논리적으로 같다는 의미를 뜻함

* 논리적 동치성 검사의 예 : `java.util.regex.Pattern`의 `equals`



#### 상위 클래스에서 재정의한 equals가 하위 클래스에도 딱 들어맞을 경우

* 상위에서 구현한 equals 로직으로 충분한 경우 재정의하는 것이 아니라 상위 클래스의 equals를 그대로 사용
  * Set의 구현체는 AbstractSet이 구현한 equals를 상속받아 사용



#### 클래스가 private이거나 package-private이고 equals를 호출할 일이 없을 경우

* equals가 실수로라도 호출되는 걸 막고 싶을 땐 아래와 같이 구현한다.

* ```java
  @Override public boolean equals(Object o) {
      throw new AssertionError();
  }
  ```



### equals의 재정의가 필요한 경우

#### 객체 식별성(두 객체가 물리적으로 같은 지)이 아니라 논리적 동치성(두 객체가 논리적으로 같은 지)이 필요한 경우

주로 두 객체가 같은지가 아니라 값이 같은지를 알기 위한 값 클래스가 여기에 포함된다.

###### 	※ 값 클래스 : Integer, String과 같이 값을 표현하는 클래스

Enum과 같이 값이 같은 인스턴스가 2개 이상 만들어지지 않는다는 보장이 되는 통제 클래스라면 equals를 재정의할 필요 없다.



#### equals 재정의 시 지켜야 하는 규약

##### 반사성(reflextivity)

객체는 자신과 같아야 한다는 뜻으로 "text".equals("text") 라는 코드는 **항상 true를 반환**해야 한다.

```java
x.equlas(x) == true
```



##### 대칭성(symmetry)

* 두 객체는 서로에 대한 동치 여부에 똑같이 답해야 한다.

```java
x.equals(y) == y.equals(x)
```

```java
public final class CaseInsensitiveString {
  private final String s;

  public CaseInsensitiveString(String s) {
    this.s = Objects.requireNonNull(s);
  }

  @Override
  public boolean equals(Object o) {
    if(o instanceof CaseInsensitiveString) {
      return s.equalsIgnoreCase(((CaseInsensitiveString) o).s);
    }

    if(o instanceof String) { //한 방향으로만 작동!!
      return s.equalsIgnoreCase((String) o);
    }
    return false;
  }
}
```

위 코드를 실행해보면 대칭성이 깨지는 것을 확인할 수 있다.

```java
public class item10 {
    public static void main(String[] args) {
        CaseInsensitiveString caseInsensitiveString = new CaseInsensitiveString("Test");
        String test = "test";
        System.out.println(caseInsensitiveString.equals(test)); //true
        System.out.println(test.equals(caseInsensitiveString)); //false
    }
}
```

##### ![1650282391449](ddamdoo_내용요약.assets/1650282391449.png)



##### 추이성(transitivity)

 첫 번째 객체와 두 번째 객체가 같고, 두 번째 객체와 세 번째 객체가 같다면, 첫 번째 객체와 세 번째 객체도 같아야 한다는 의미

* null이 아닌 모든 참조 값 x, y, z에 대해 x.equals(y)와 y.equals(z)가 true면 x.equals(z)도 true이다. 



```java
package chap3;

public class Point {
    private final int x;
    private final int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public boolean equals(Object o) {
        if (!(o instanceof Point))
            return false;
        Point p = (Point)o;
        return p.x == x && p.y == y;
    }
}
```
```java
package chap3;

import java.awt.*;

public class ColorPoint extends Point {
    private final Color color;

    public ColorPoint(int x, int y, Color color) {
        super(x, y);
        this.color = color;
    }

    @Override
    public boolean equals(Object o) {
        if (!(o instanceof Point))
            return false;

        if (!(o instanceof ColorPoint))
            return o.equals(this);
        //Point와 비교 시 색상을 무시하고 있다.
        return super.equals(o) && ((ColorPoint) o).color == color;
    }

    public static void main(String[] args) {
        ColorPoint redPoint = new ColorPoint(1, 2, Color.RED);
        Point point = new Point(1, 2);
        ColorPoint bluePoint = new ColorPoint(1, 2, Color.BLUE);

        System.out.println("redpoint와 point 비교 " + redPoint.equals(point)); //true
        System.out.println("point와 bluepoint 비교 " + point.equals(bluePoint)); //true
        System.out.println("redpoint와 bluepoint 비교 " + redPoint.equals(bluePoint)); //false
    }
}
```

이 코드를 실행 한 결과는 아래와 같다.

##### ![1650283842219](ddamdoo_내용요약.assets/1650283842219.png)

redPoint와 point와의 비교에서, 그리고 point와 bluePoint와의 비교는 색상을 무시했지만, redPoint와 bluePoint와의 비교에서는 색상을 고려했기 때문에 추이성이 깨진 것을 알 수 있다. 이러한 문제는 무한재귀에 빠질 수 있기에 조심해야 한다. 

추이성에 위배되는 문제를 지키기 위한 대안은 상속 대신 **컴포지션을 사용**하는 것이 있다. Point를 상속하는 대신 Point 객체를 필드로 만든 뒤 Point 뷰 메서드를 만드는 방법이다. 코드는 아래와 같다.

```java
public class ColorPointFromComposition {
    private final Point point;
    private final Color color;

    public ColorPointFromComposition(int x, int y, Color color) {
        this.point = new Point(x, y);
        this.color = color;
    }

    public Point asPoint() {
        return point;
    }

    @Override
    public boolean equals(Object o) {
        if(!(o instanceof ColorPointFromComposition))return false;
        ColorPointFromComposition cp = (ColorPointFromComposition) o;
        return cp.point.equals(point) && cp.color.equals(color);
    }
}
```



##### 일관성(constistency)

* null이 아닌 모든 참조 값 x, y에 대해 x.equals(y)를 반복해서 호출하면 항상 같은 값을 반환해야 한다. 만약 클래스를 불변으로 만들었다면 equals에서 나온 결과가 계속 같은 값을 반환해야 한다. 클래스가 반환 결과에 신뢰할 수 없는 자원이 들어가선 안된다. ex) ip 주소



##### nullable

* null이 아닌 모든 참조 값 x에 대해서, x.equals(null)은 false여야 한다.
* true만 반환하지 않으면 된다는게 아니라 NullPointerException도 발생시켜서는 안된다.



### equals 메소드 구현방법

1. ##### ==연산자를 사용해 입력이 자기 자신의 참조인지(반사성) 확인

   * 자기자신이면 true를 반환하여 성능 최적화를 할 수 있도록 함

2. ##### instanceof 연산자로 입력이 올바른 타입인지 확인

3. ##### 입력을 올바른 타입으로 형변환

4. ##### 입력 객체와 자기 자신의 대응되는 핵심필드들이 모두 일치하는지 하나씩 검사

5. ##### equals를 재정의할 땐 hashCode도 반드시 재정의



---



## Item 11. equals를 재정의 하려거든 hashCode도 재정의하라

##### equals : 두 객체의 내용이 같은지, 동등성(equality)를 비교하는 메서드

##### hashCode : 두 객체가 같은 객체인지, 동일성(identity)를 비교하는 메서드



#### Object 명세에서 발췌한 HashCode 규약

1. 애플리케이션이 유지되는 동안 equals비교에 사용되는 정보가 변경되지 않았다면, 몇 번을 호출하더라도 일관된 값을 반환해야 한다.
2. equals(Object)가 동일하다고 판단되면 두 객체의 hashCode는 동일한 값을 반환해야 한다.
3. equals(Object)가 동일하지 않다고 판단하더라도 hashCode가 서로 다른 값을 반환할 필요는 없다.  하지만, 다른 값을 반환해야 해시테이블의 성능이 좋아진다.



```java
public static void main(String[] args) {
	Map<PhoneNumber, String> m = new HashMap<>();
	m.put(new PhoneNumber(707, 867, 5309), "Jenny");

	System.out.println(m.get(new PhoneNumber(707, 867, 5309)));
}
```

책에 나온 예시 사례이다. 이 코드는 Jenny라는 값이 반환될거라는 기대가 있지만 실제로는 `null`이 반환된다. 여기서 HashMap에 Jenny를 넣을 때와 이것을 꺼내려할 때 총 2번의 PhoneNumber 인스턴스가 사용되었기 때문이다. 이러한 문제를 해결하려면 PhoneNumber에 대해서 적절한 hashCode를 작성해주면 해결이 가능하다.



##### 사용해서는 안되는 방법

```java
@Override public int hashCode() { 
    return 42; 
}
```

위 코드대로 사용하게 되면 모든 객체가 해시테이블의 버킷 하나에 담겨 LinkedList 처럼 동작하게 된다. 그렇게 되면 평균 수행 시간이 O(1)인 해시테이블의 성능이 O(n)으로 느려져서 객체가 많아지면 쓸 수 없게 된다.

좋은 해시 함수라면 세번 째 규약에서 말하듯이 서로 다른 인스턴스에 다른 해시코드를 반환해야 한다. 



#### 좋은 hashCode 를 작성하는 간단한 요령

1. int 변수 reult 를 선언 한 후 값 c로 초기화 한다. (이때 c는 해당 객체의 첫번째 핵심 필드를 2.a단계 방식으로 계산한 hashCode)

2. 해당 객체의 나머지 핵심필드 f 각각에 대해 다음 작업을 수행

   a. 해당 필드의 해시코드 c를 계산

   1. 기본 타입 필드라면, Type.hashCode(f)를 수행
   2. 참조 타입 필드면서 이 클래스의 equals 메서드가 이 필드의 equals를 재귀적으로 호출해 비교한다면, 이 필드의 hashCode 를 재귀적으로 호출
      * 계산이 더 복잡해질 것 같으면, 이 필드의 표준형을 만들어 그 표준형의 hashCode를 호출하고, 이 때  필드의 값이 null이면 일반적으로 0을 사용
   3. 필드가 배열이라면, 핵심 원소 각각을 별도 필드처럼 다룬다. 이상의 규칙을 재귀적으로 적용해 각 핵심 원소의 해시코드를 계산한 다음, 2.b단계 방식으로 갱신한다. 배열에 핵심 원소가 하나도 없다면 단순히 상수(0을 추천)를 사용한다. 모든 원소가 핵심 원소라면 Arrays.hashCode를 사용한다.

   b. 2.a 단계에서 계산한 해시코드 c 로 result 를 갱신한다.

   $result = 31 * result + c;$

3. result 를 반환



##### ※ 위에 해쉬코드를 작성하는 요령에서 31이라는 숫자가 무심코 나왔는데 이는 31이  홀수이면서 소수이기 때문이다.

- 곱할 숫자가 짝수고 오버플로가 발생하면 정보를 잃게되기 때문
  - 2를 곱하면 시프트 연산과 같은 결과를 줌
- 소수를 곱하는 것은 전통적으로 나머지 연산에서 충돌을 줄이기 위한 방법으로 주로 사용되는 방식이다.
- 31을 곱하는 것은 시프트 연산과 뺄셈으로 대체해 최적화 할 수 있다.
  - $31*i$와 $(i<<5)-i$는 같은 뜻으로 사용된다.



```java
@Override public int hashCode() {
	int result = Short.hashCode(areaCode);
	result = 31 * result + Short.hashCode(prefix);
	result = 31 * result + Short.hashCode(lineNum);
	return result;
}

public static void main(String[] args) {
	Map<PhoneNumber, String> m = new HashMap<>();
	m.put(new PhoneNumber(707, 867, 5309), "Jenny");

	System.out.println(m.get(new PhoneNumber(707, 867, 5309)));
}
```



추가적으로 해시 충돌이 더욱 적은 방법을 사용하기 위해서는 `com.google.common.hash.Hashing`을 참고하면 된다.

>https://guava.dev/releases/21.0/api/docs/com/google/common/hash/Hashing.html



Object 클래스는 `hash` 라는 임의의 개수만큼 객체를 받아 해시코드로 계산해주는 메소드를 제공한다.

* 입력 인수를 담기 위한 배열을 만들고, 박싱/언박싱을 거쳐야 하기 때문에 속도는 조금 느리다.



클래스가 불변이고 hashCode를 계산하는 비용이 크다면 `캐싱` 고려하는 것이 좋다. 클래스의 객체가 주로 해시의 키로 사용된다면 인스턴스가 생성될 때 해시코드 계산해야 한다. 해시 키로 사용되지 않으면 hashCode가 처음 불릴 때 계산하는 전략을 사용한다.(지연초기화 전략)



```java
private int hashCode;

@Override public int hashCode() {
	int result = hashCode;
	if(result == 0){
		result = Short.hashCode(areaCode);
		result = 31 * result + Short.hashCode(prefix);
		result = 31 * result + Short.hashCode(lineNum);
		hashCode = result;
	}
	return result;
}
```

시스템의 성능을 높이기 위해서 해시코드 계산시에 핵심 필드를 생략하면 안된다. 이는 속도가 빨라질 수도 있지만 해시의 품질을 떨어뜨려 더 심각한 성능 문제를 만들 수 있다.

또한 hashCode가 반환하는 값의 생성 규칙에 대해서 API 사용자에게 자세히 알리지 않아야 추후 결함이나 기능개선을 위해 계산 방식을 바꿀 일이 생길 때 수정이 가능하다.