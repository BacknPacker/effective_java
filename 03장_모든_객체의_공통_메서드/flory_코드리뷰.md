# 3장. 모든 객체의 공통 메서드

## ✨ 아이템10. equals는 일반 규약을 지켜 재정의하라.
> 아이템10. equals는 일반 규약을 지켜 재정의하라. 의 올바른 코드는 아이템11 hashCode도 재정의 해야한다와 이어지므로 다음 챕터에서 하나의 예제로 확인하자.

## ✨ 아이템11. equals를 재정의하려거든 hashCode도 재정의하라.
#### ✔️ 이전 코드
```java
public class Car {
  private final String name;

  public Car(String name) {
    this.name = name;
  }

  @Override
  public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;
    Car car = (Car) o;
    return Objects.equals(name, car.name);
  }

  public static void main(String[] args){
    Car car1 = new Car("foo");
    Car car2 = new Car("foo");
    
    System.out.println(car1.equals(car2)); // true

    // ----------------------------
    
    List<Car> cars = new ArrayList<>();
    cars.add(new Car("foo"));
    cars.add(new Car("foo"));

    System.out.println(cars.size()); // 2
    
    // -----------------------------
    
    Set<Car> cars = new HashSet<>();
    cars.add(new Car("foo"));
    cars.add(new Car("foo"));

    System.out.println(cars.size()); // 2 (hashSet은 중복을 허용하지 않는데도 2가나옴)
  }
}
```

<br>

#### ✔️ 변경 후 코드
```java
public class Car {
  private final String name;

  public Car(String name) {
    this.name = name;
  }

  @Override
  public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;
    Car car = (Car) o;
    return Objects.equals(name, car.name);
  }

  @Override
  public int hashCode() {
    return Objects.hash(name);
  }
}
```
<br>

#### ✔️ 코드리뷰 정리
> hashCode 메서드의 리턴 값이 우선 일치하고 equals 메서드의 리턴 값이 true여야 논리적으로 같은 객체라고 판단한다.
하지만 Car 클래스에는 hashCode 메서드가 재정의 되어있지 않아서 Object 클래스의 hashCode 메서드가 사용되었다.  
> Object 클래스의 hashCode 메서드는 객체의 고유한 주소 값을 int 값으로 변환하기 때문에 객체마다 다른 값을 리턴한다. 두 개의 Car 객체는 equals로 비교도 하기 전에 서로 다른 hashCode 메서드의 리턴 값으로 인해 다른 객체로 판단된 것이다.  
> 그러므로 equals를 사용할때는 hashCode도 반드시 재정의해서 사용해야 이런 예상치못한 오류의 발생을 피할 수 있다.

## ✨ 아이템12. toString을 항상 재정의하라.
***
#### ✔️ 이전 코드
```java
class Car {
  final String name;
  final int position;

  Car(String name, int position) {
    this.name = name;
    this.position = position;
  }

  public static void main(String[] args){
    Car car = new Car("flory", 0);
    System.out.println(car); // Main$1Car@2c7b84de
  }
}
```
<br>

#### ✔️ 변경 후 코드
```java
class Car {
  final String name;
  final int position;

  Car(String name, int position) {
    this.name = name;
    this.position = position;
  }

  @Override
  public String toString() {
    return String.format("자동차 (이름 = %s, 위치 = %s)", name, position);
  }

  public static void main(String[] args){
    Car car = new Car("flory", 0);
    System.out.println(car.toString()); // 자동차 (이름 = flory, 위치 = 0)
  }
}
```
<br>

#### ✔️ 코드리뷰 정리
toString()을 재정의 하지 않을경우 16진수의 값으로 알아보기 힘들게 나온다. 따라서 항상 toString()을 재정의하여 사용하도록하자.


## ✨ 아이템13. clone 재정의는 주의해서 진행하라.

#### 코드
```java
public class Coffee implements Cloneable {
  @Override
  public synchronized Coffee clone() {
    Coffee clone;
    try {
      clone = (Coffee) super.clone();
    } catch (CloneNotSupportedException e) {
      throw new AssertionError();
    }
    return clone;
  }
}
```
<br>

#### ✔️ 코드리뷰 정리
이 Coffee 클래스의 하위 클래스에서 super.clone()을 호출한다면 잘못된 클래스의 객체가 만들어져, 결국 하위 클래스의 clone() 메서드가 제대로 동작하지 않게 된다.
상위 클래스의 clone() 값을 반환하면 안되고, 하위 클래스 타입으로 변환한 뒤 반환 해야 한다.


## ✨ 아이템14. `Comparable을 구현할지 고려하라.`
***
#### ✔️ 코드
```java
public class WordList {
  public static void main(String[] args) {
    Set<String> s = new TreeSet<>();
    Collections.addAll(s,args);
    System.out.println(s);
  }
}
```

#### ✔️ 코드리뷰
검색, 극단값 계산, 자동 정렬되는 컬렉션 관리에 유용하다.

