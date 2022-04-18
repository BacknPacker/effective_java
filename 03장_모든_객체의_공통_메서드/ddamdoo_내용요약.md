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