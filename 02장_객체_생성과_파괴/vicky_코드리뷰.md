# 2장. 객체 생성과 파괴

## 아이템1. 생성자 대신 정적 팩터리 메서드를 고려하라.
<br>

#### 이전 코드
```java


public Account(string name,int price){
this.name=name;
this.price=price;

}
```
<br>

#### 변경 후 코드
```java

public static Boolean ValueOf(boolean b){
return b? Boolean.True:Boolean.False;

}
```
<br>

#### 코드리뷰 정리
