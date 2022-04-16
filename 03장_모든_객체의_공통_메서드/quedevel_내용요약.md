# 2장. 객체 생성과 파괴

## 아이템10. equals는 일반 규약을 지켜 재정의하라.
+ 클래스는 생성자와 별도로 정적 팩터리 메서드를 제공할 수 있다.
+ 디자인 패턴의 팩터리 메서드와 무관하다.
```java
public static Boolean valueOf(boolean b){
  return b ? Boolean.TRUE : Bloolean.FALSE;
}
```
### 정적 팩터리 메서드의 장점
1. 반환 객체 특성을 제대로 묘사하는 이름을 지을 수 있다.
    + BigInteger(int,int,Random) vs BigInteger.probablePrime
    + 동일한 시그니처를 가지는 메서드를 만들고자 할 때

## 아이템11. equals를 재정의하려거든 hashCode도 재정의하라.
## 아이템12. toString을 항상 재정의하라.
## 아이템13. clone 재정의는 주의해서 진행하라.
## 아이템14. Comparable을 구혈할지 고려하라.