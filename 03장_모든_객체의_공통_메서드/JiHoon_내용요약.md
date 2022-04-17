# 3장 모든 객체의 공통 메서드
모든 객체의 조상은 Object 클래스이다.  
Object에서 final이 아닌 메서드는 모두 재정의(overriding)를 염두에 두고 설계됐다.  
하지만 재정의를 일반 규약에 맞지 않게 구현하면 의도와 다르게 동작할 수 있다.

> 우리는 이런 메서드들을 언제 어떻게 재정의해야 하는지를 알아야한다.

## 아이템 10. equals는 일반 규약을 지켜 재정의하라.
> equals는 재정의하기 쉬워 보이지만 곳곳에 함정이 있다.  
> 이런 함정을 피하는 가장 쉬운길은 아예 재정의하지 않는다.

### ✅ equals를 재정의 하면 안되는 경우.
#### 1. 각 인스턴스가 본질적으로 고유할 때
- 값이 아닌 동작하는 개체 인스턴스는 동일한 인스턴스가 존재하지 없기에 Object의 equals로 충분하다.  
- 예) Tread

#### 2. 인스턴스의 논리적 동치성(ogical equality)를 검사할 일이 없을 때
- 값을 비교해서 동등한지 비교할 일이 없다 == 논리적 동치성 검사 할 일이 없다.
- 기본적인 Object의 equals로 충분하다.

#### 3. 상위 클래스에서 재정의한 equals가 하위 클래스에도 들어맞을 때
- 상위에서 구현한 equals로직으로 사용하면 된다.
- 예) `Set` 구현체는 `AbstractSet`이 구현한 equals를 상속받아 쓴다.

#### 4. 클래스가 priviate이거나 package-private이고 equals 메서드를 호출할 일이 없다.

```java
// equals가 실수로라도 호출되는 걸 막고싶다면 아래와같이 재정의해주면 된다.
@Override public boolean equals(Object o) {
  throw new AssertionError(); // 호출 금지!
}
```

<hr/>

```java

```

## 아이템 11. equals를 재정의하려거든 hashCode도 재정의하라.


## 아이템 12. toString을 항상 재정의하라.

## 아이템 13. clone 재정의는 주의하여 진행하라.

## 아이템 14. Comparable를 구현할지 고려하라.


