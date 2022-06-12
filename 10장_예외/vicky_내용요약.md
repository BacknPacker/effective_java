# 10장. 예외
> 예외를 제대로 활용한다면 프로그램의 가독성, 신뢰성, 유지보수성이 높아지지만, 잘못 사용한다면 반대의 효과만 나타난다.

## 아이템 69. 예외는 진짜 예외 상황에만 사용하라
### 핵심내용
+ 예외는 오직 예외 상황에서만 써야 한다. 절대로 일상적인 제어 흐름용으로 쓰여선 안 된다.
+ 잘 설계된 API라면 클라이언트가 정상적인 제어 흐름에서 예외를 사용할 일이 없게 해야 한다.
### 잘못된 사례 - 무한루프에서 배열 끝을 예외로 처리한 케이스.
```java
try {
  int i = 0;
  while(true) {
    range[i++].climb();
  }
} catch (ArrayIndexOutOfBoundsException e) { }
```
#### 취지
+ JVM은 배열을 접근할 때마다 경계를 넘지 않는지 검사하는데, 일반적인 반복문도 배열의 경계에 도달하면 종료한다.
#### 결과 
+ 중복된 일을 하지 않기 위해 예외처리를 넣었으나, 예외를 사용한 쪽이 표준 관용구보다 훨씬 느리다.
#### 잘못된 이유
1. 예외는 예외 상황에 쓸 용도로 설계되었으므로 JVM 구현자 입장에서는 명확한 검사만큼 빠르게 만들어야 할 만큼 최적화에 신경쓰지 않았을 확률이 높다.
2. 코드를 try-catch 블록 안에 넣으면 JVM이 적용할 수 있는 최적화가 제한된다.
3. 배열을 순회하는 표준 관용구는 앞서 걱정한 중복 검사를 수행하지 않는다. JVM이 알아서 최적화해서 없애준다.
### '상태 의존적' 메서드를 제공하는 클래스는 '상태 검사' 메서스도 함께 제공하자
+ Iterator 인터페이스의 next와 hasNext
+ 차안 - 빈 옵셔널이나 null을 반환하는 것
#### 선택하는 지침
1. 외부 동기화 없이 여러 스레드가 동시 접근 가능하거나 상태가 변할 수 있다면 옵셔널을 사용한다.
2. 성능이 중요한 상황에서 상태 검사 메서드가 상태 의존적 메서드의 작업 일부를 중복 수행한다면 옵셔널 반환을 선택한다.
3. 다른 모든 경우에는 상태 검사 메서드 방식이 더 낫다.

<br><hr><br>

## 아이템 70. 복구할 수 있는 상황에는 검사 예외를, 프로그래밍 오류에는 런타임 예외를 사용하라
### 핵심내용
+ 검사예외, 런타임예외, 에러
+ 복구가 가능하다고 판단되면 검사예외를, 그렇지 않다면 런타임 예외, 확신이 어렵다면 비검사 예외를 선택해라.
+ 검사 예외도 아니고 런타임 예외도 아닌 throwable은 정의하지도 말자.
+ 검사 예외라면 복구에 필요한 정보를 알려주는 메서드도 제공하자.
#### 호출하는 쪽에서 복구하리라 여겨지는 상황이라면 검사 예외를 사용해라 
+ API 설계자는 API 사용자에게 검사 예외를 던져주어 그 상황에서 회복해내라고 요구한 것 
#### (비검사 Thowable) 프로그래밍 오류를 나타낼 때는 런타임 예외를 사용하자
+ 대부분 전제조건을 만족하지 못했을 때 발생한다.
+ ArrayIndexOutOfBoundsException
   + 배열의 인덱스는 0 ~ n-1 사이어야 한다. ArrayIndexOutOfBoundsException 발생은 전제조건이 지켜지지 않았다는 뜻이다.
   + 복구할 수 있는 프로그래밍 오류인지 명확하지 않다.
   + API 설계자의 판단에 따라, 복구가 가능하다고 판단되면 검사예외를, 그렇지 않다면 런타임 예외, 확신이 어렵다면 비검사 예외를 선택해라. 
#### (비검사 Thowable) 에러는 보통 JVM이 자원부족, 불변식 깨짐 등 더 이상 수행을 계속할 수 없는 상황일 때 사용한다.
+ 자바 언어 명세 요구사항 X
+ Error 클래스를 상속해 하위 클래스를 만드는 일을 자제하는 것이 좋다. -> 모두 RuntimeException의 하위 클래스여야 한다.
+ Error는 상속하지 말아야할 뿐 아니라, throw 문을 직접 던지는 일도 없어야한다.(AssertionError는 예외)
+ Exception, RuntimeException, Error를 상속하지 않은 throwble을 만들 수 있는데 이런, throwable은 이로울 게 없으니 절대 사용하지 말아라.

<br><hr><br>

## 아이템 71. 필요 없는 검사 예외 사용은 피하라
### 핵심내용
+ 검사 예외는 안전성을 높여주지만 남용하면 사용하기 불편한 API가 된다.
+ 비검사 예외를 던지거나, 옵셔널 반환을 고려하자.
+ 옵셔널만으로는 상황을 처리하기 충분하지 않은 경우는 검사 예외를 던지자.
### 검사 예외를 회피하는 방법
#### 1. 절절한 옵셔널을 반환
#### 2. 검사예외를 던지는 메서드를 2개로 쪼개서 비검사 예외로 바꾼다.
```java
try {
  obj.action(args);
} catch (TheCheckedException e) { }
```
```java
if (obj.actionPermitted(args)) {
obj.action(args);
} else { }
```
+ 실제로 1줄로 쓰이거나, 실패시 스레드 중단을 원하는 경우
+ 외부 동기화가 없기 때문에 외부 요인에 의해 상태가 변경될 수 있다면 적절하지 않다. 또한 상태 검사 메서드가 작업 일부를 중복수행해서 발생하는 성능 손해도 있을 수 있다.
```java
obj.action(args);
```

<br><hr><br>

## 아이템 72. 표준 예외를 사용하라
### 표준예외 사용의 장점
+ 많은 프로그래머들에게 익숙해진 규약
+ 가독성이 좋고, 메모리 사용량도 줄이고 클래스 적재시간도 적게 걸린다.
### 자주 사용하는 에외들
#### IllegalArgumentException
+ 허용하지 않는 값이 인수로 건네졌을 때 (null은 따로 NullPointerException으로 처리)
#### IllegalStateException
+ 객체가 메서드를 수행하기에 적절하지 않은 상태일 때
#### NullPointerException
+ null을 허용하지 않는 메서드에 null을 건넸을 때
#### IndexOutOfBoundsException
+ 인덱스가 범위를 넘어섰을 때
#### ConcurrentModificationException
+ 허용하지 않는 동시 수정이 발견됐을 때
#### UnsupportedOperationException
+ 호출한 메서드를 지원하지 않을 때
+ 원소를 넣을 수만 있는 List 구현체에 대고 누군가 remove 메서드를 호출하면 이 예외를 던질 것이다.
  
<br><hr><br>


## 아이템 73. 추상화 수준에 맞는 예외를 던지라
### 예외번역(exception translation)
+ 상위 계층에서는 저수준의 예외를 잡아 자신에 추상화 수준에 맞는 예외를 던져야 된다.
```java
try {
} catch (LowerException e) {
    throw new HigherException(...);
}
```
#### AbstractSequentialList에서 수행하는 예외 번역의 예
+ List 인터페이스의 골격 구현
```java
/**
* Returns the element at the specified position in this list.
*
* <p>This implementation first gets a list iterator pointing to the
* indexed element (with {@code listIterator(index)}).  Then, it gets
* the element using {@code ListIterator.next} and returns it.
*
* @throws IndexOutOfBoundsException {@inheritDoc}
*/
public E get(int index) {
  try {
      return listIterator(index).next();
  } catch (NoSuchElementException exc) {
      throw new IndexOutOfBoundsException("Index: "+index);
  }
}
```
### 예외번역(exception translation)
+ 예외를 번역할때, 저수준 예외가 디버깅에 도움이 된다면 예외 연쇄(exception chaining)를 사용하는게 좋다.
+ 예외 연쇄란, 문제의 근본 원인(cause)인 저수준 예외를 고수준 예외에 실어 보내는 방식이다. 그러면 별도의 접근자 메서드(Throwable의 getCause 메서드)를 통해 필요하면 언제든 저수준 예외를 꺼내볼 수 있다.
```java
class HigherLevelException extends Exception {
    HigherLevelException(Throwable cause) {
        super(cause);
    }
}
```
### 로깅기능
+ 아래 계층에서의 예외를 피할 수 없다면, 상위 계층에서 그 예외를 조용히 처리하여 문제를 API 호출자에까지 전파하지 않는 방법이 있다. 
+ 이 경우 발생하는 예외는 java.util.logging 같은 적절한 로깅 기능을 활용하여 기록해두면 좋다. 
+ 클라이언트 코드와 사용자에게 문제를 전파하지 않으면서도 프로그래머가 로그를 분석해 추가 조취를 취할 수 있게 해준다.

<br><hr><br>

## 아이템 74. 메서드가 던지는 모든 예외를 문서화하라
### @throws
+ 상위 클래스 하나로(Throwable, Exception) 묶어서 문서화하지 말자
+ main 클래스에서는 오직 JVM만이 호출하므로 Exception 선언이 허용된다.
### 비검사예외의 문서화
+ 비검사 예외는 메서드 선언의 throws 목록에 넣지 말자
+ 문서화가 불가능한 경우
   + 다른 사람이 작성한 클래스를 사용해서 메서드를 만들었을 때 비검사 예외의 발생
   + 클래스 설명에 추가하는 것도 방법

<br><hr><br>

## 아이템 75. 예외의 상세 메시지에 실패 관련 정보를 담으라
+ 실패 순간을 포착하려면 발생한 예외에 관여된 모든 매개변수와 필드의 값을 실패 메시지에 담아야 한다.
+ 포착한 실패 정보는 예외 상황을 복구하는 데 유용할 수 있으므로 접근자 메서드(lower, upperBound, index)는 비검사 예외보다는 검사 예외에서 더 빛을 발한다.
```java
/**
 * IndexOutOfBoundsException을 생성한다.
 *
 * @param lowerBound 인덱스의 최소값
 * @param upperBound 인덱스의 최대값 + 1
 * @param index 인덱스의 실제값
 */
public IndexOutOfBoundsException(int lowerBound, int upperBound, int index) {
   // 실패를 포착하는 상세 메시지를 생성한다.
   super(String.format("최소값: %d, 최대값: %d, 인덱스: %d", lowerBound, upperBound, index));
   
   // 프로그램에서 이용할 수 있도록 실패 정보를 저장해둔다.
   this.lowerBound = lowerBound;
   this.upperBoudn = upperBound;
   this.index = index;
}
```

<br><hr><br>

## 아이템 76. 가능한 한 실패 원자적으로 만들라
### 실패 원자적
+ 호출된 메소드가 실패하더라도 해당 객체는 메서드 호출 전 상태를 유지해야 한다는 특성
#### 1. 불변 객체로 만든다.
#### 2. 가변 객체의 경우, 객체 변경 전의 잠재적 예외 가능성을 걸러낸다.
```java
public Object pop() {
  if (size == 0) {
      throw new EmptyStackException();
  }

  Object result = elements[--size];
  elements[size] = null; // 다 쓴 참조 해제
  return result;
}
```
#### 3. 객체의 임시 복사본에서 작업을 수행한 다음, 작업이 성공적으로 완료되면 원래 객체와 교체한다.
+ 데이터를 임시 자료구조에 저장해 작업하는게 더 빠를때 적용하기 좋은 방식이다.
#### 4. 작업 도중 발생하는 실패를 가로채는 복구 코드를 작성하여 작업 전 상태로 되돌린다.
+ 주로 (디스크 기반의) 내구성을 보장해야하는 자료구조에 쓰이는데 자주 쓰이는 방법은 아니다.

<br><hr><br>

## 아이템 77. 예외를 무시하지 말라
```java
try{ }
catch(SomeException e){ }
```
+ 예외를 무시하면 예외가 존재할 이유가 없다.
+ 다른 문제를 발생시킬 가능성이 있다.
+ 만약 예외를 무시하기로 결정했으면 이유를 주석으로 남기고 예외 변수 이름도 ignored로 바꿔놓도록 하자.
+ 바깥으로 전파되게만 나둬도 최소한 디버깅 정보를 남긴 채 프로그램이 신속히 중단되게 할 수 있다.

