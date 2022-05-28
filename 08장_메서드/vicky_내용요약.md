# 8장. 메서드


## 아이템 49. 매개변수가 유효한지 검사하라
+ 매개변수의 유효성은 실행 전 검사되어야 한다.
+ 단언문을 사용하면 특별한 에러를 나타내지 않지만 vm 옵션을 주면 에러를 보여준다.
##### null 여부를 검사하는 메서드
```java
 public Foo(Bar bar) {
     this.bar = Objects.requireNonNull(bar);
     this.bar = Objects.requireNonNull(bar, "bar must not be null");
     this.bar = Objects.requireNonNullElse(bar, "");
 }
```
+ 검사를 하지 않아도 되는 경우는 유효성 검사비용이 지나치게 높거나 실용적이지 않은 경우 또는 계산과정에서 암묵적으로 검사가 수행되는 경우를 말한다.

<br><hr><br>

## 아이템 50. 적시에 방어적 복사본을 만들라
### 자바는 안전하다는 생각을 버리자.
+ 네이티브 메서드를 사용하지 않는다. -> 아이템 66
  + 네이티브 메서드란 C나 C++ 같은 네이티브 프로그래밍 언어로 작성한 메서드를 말한다.
  + 네이티브 언어는 컴파일러 언어 중에서 컴파일러의 결과물이 특정 CPU의 기계어인 경우의 언어. 
### 가변 클래스 Date의 문제점
+ Date 클래스의 대부분 메서드는 사용중단되었으므로 사용하면 안된다. (Calendar 클래스도 동일한 문제점)
+ Java 8부터 제공되는 LocalDateTime, ZonedDateTime과 같은 클래스를 사용하는 것을 권장
```java
public final class Period {
    private final Date start;
    private final Date end;
    
    public Period(Date start, Date end) {
        if (start.compareTo(end) > 0)
            throw new IllegalArgumentException(
                    start + "가 " + end + "보다 늦다.");
        this.start = start;
        this.end   = end;
    }

    public Date start() {
        return start;
    }
    public Date end() {
        return end;
    }

    public String toString() {
        return start + " - " + end;
    }
    
    // Date는 가변임으로 p 내부가 수정가능해진다.
    Date start = new Date();
    Date end = new Date();
    Period p = new Period(start, end);
    end.setYear(20);
```
### 방어적 복사
+ 매개변수 변환을 시도하려는 외부 공격을 막기 위해서는 방어적 복사가 필요하다.
+ 생성자를 사용한 방어적 복사
+ 멀티스레딩 환경이라면 원본 객체의 유효성을 검사한 후 검사본을 만드는 찰나의 순간에 다른 스레드가 원본 객체를 수정할 위험이 있기 때문이다.
```java
public Period(Date start, Date end) {
    this.start = new Date(start.getTime());
    this.end = new Date(end.getTime());
    
    if (this.start.compareTo(this.end) > ) 
        throw new IllegalArgumentException(this.start + " after " + this.end);
}
```
```java
public Date start() {
    return new Date(start.getTime());
}
public Date end() {
    return new Date(end.getTime());
}
```
### 결론
+ 메서드든 생성자든 클라이언트가 제공한 객체의 참조를 내부의 자료구조에 보관해야 할 때면 그 객체가 잠재적으로 변경될 수 있는지를 생각해야 한다. 확신할 수 없다면 복사본을 만들어 저장해야 한다.
+ 길이가 1이상인 배열은 무조건 가변임을 잊지말자.
+ 호출하는 쪽에서 내부 요소를 수정하지 않는다는 보장이 있다면 방어적 복사를 생략할 수도 있다.

<br><hr><br>

## 아이템 51. 메서드 시그니처를 신중히 설계하라
+

<br><hr><br>

## 아이템 52. 다중정의는 신중히 사용하라
+

<br><hr><br>

## 아이템 53. 가변인수는 신중히 사용하라
+

<br><hr><br>

## 아이템 54. null이 아닌, 빈 컬렉션이나 배열을 반환하라
+

<br><hr><br>

## 아이템 55. 옵셔널 반환은 신중히 하라
+

<br><hr><br>

## 아이템 56. 공개된 API 요소에는 항상 문서화 주석을 작성하라
+
