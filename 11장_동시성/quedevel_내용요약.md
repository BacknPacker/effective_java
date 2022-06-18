# 🔥 [ Chapter11 ] 동시성

## 🎯  아이템 78. 공유 중인 가변 데이터는 동기화해 사용하라.
synchronized 키워드는 해당 메서드나 블록을 한번에 한 스레드씩 수행하도록 보장한다. <br>
한 개체가 일관된 상태를 가지고 생성되고, 이 객체에 접근하는 메서드는 그 객체에 락(lock)을 건다. <br>
락을 건 메서드는 객체의 상태를 확인하고 필요하면 수정한다.<br>
즉, 객체를 하나의 일관된 상태에서 다른 일관된 상태로 변화시킨다.<br>

<br>

동기화 없이는 한 스레드가 만든 변화를 다른 스레드에서 확인하지 못할 수 있다.<br>
동기화는 일관성이 깨진 상태를 볼 수 없게 하는 것은 물론, 동기화된 메서드나 블록에 들어간<br>
스레드가 같은 락의 보호하에 수행된 모든 이전 수정의 최종 결과를 보게 해준다. <br>
**_동기화는 배타적 실행뿐 아니라 스레드 사이의 안정적인 통신에 꼭 필요하다._**

* 잘못된 코드 - 이 프로그램은 얼마나 오래 실행될까?
```java
public class StopThread {

    private static boolean stopRequested;

    public static void main(String[] args) throws InterruptedException {
        Thread backgroundThread = new Thread(() -> {
            int i = 0;
            while (!stopRequested){
                i++;
            }
        });
        backgroundThread.start();

        TimeUnit.SECONDS.sleep(1);
        stopRequested = true;

    }
}
```

<br>

* 적절히 동기화해 스레드가 정상 종료한다.
```java
public class StopThread {

    private static boolean stopRequested;

    private static synchronized void requestStop(){
        stopRequested = true;
    }

    private static synchronized boolean stopRequested(){
        return stopRequested;
    }

    public static void main(String[] args) throws InterruptedException {
        Thread backgroundThread = new Thread(() -> {
            int i = 0;
            while (!stopRequested()){
                i++;
            }
        });
        backgroundThread.start();

        TimeUnit.SECONDS.sleep(1);
        requestStop();
    }
}
```

<br>

* `volatile` 필드를 사용해 스레드가 정상 종료한다.<br>
  volatile 키워드를 추가하게 되면 메인 메모리에 저장하고 읽어오기 때문에 변수 값 불일치 문제를 해결 할 수 있습니다.<br>
```java
public class StopThread {

    private static volatile boolean stopRequested;

    public static void main(String[] args) throws InterruptedException {
        Thread backgroundThread = new Thread(() -> {
            int i = 0;
            while (!stopRequested){
                i++;
                System.out.println(i);
            }
        });
        backgroundThread.start();

        TimeUnit.SECONDS.sleep(1);
        stopRequested = true;
    }
}
```
**_여러 스레드가 가변 데이터를 공유한다면 그 데이터를 읽고 쓰는 동작은 반드시 동기화 해야한다.**_


## 🎯  아이템 79. 과도한 동기화는 피하라.
**_응답 불가와 안전 실패를 피하려면 동기화 메서드나 동기화 블록 안에서는 제어를 절대로 클라이언트에 양도하면 안된다._** <br>
동기화된 영역을 포함한 클래스 관점에서는 이런 메서드는 모두 바깥 세상에서 온 외계인이므로 예측할 수 없는 이슈를 발생 시킨다 <br>

* 잘못된 코드. 동기화 블록 안에서 외계인 메서드를 호출한다.
```java
public class ObservableSet<E> extends ForwardingSet<E> {
    public ObservableSet(Set<E> set) { super(set); }

    private final List<SetObserver<E>> observers = new ArrayList<>();

    public void addObserver(SetObserver<E> observer) {
        synchronized(observers) {
            observers.add(observer);
        }
    }

    public boolean removeObserver(SetObserver<E> observer) {
        synchronized(observers) {
            return observers.remove(observer);
        }
    }

    private void notifyElementAdded(E element) {
        synchronized(observers) {
            for (SetObserver<E> observer : observers)
                observer.added(this, element);
        }
    }

    @Override 
    public boolean add(E element) {
        boolean added = super.add(element);
        if (added)
            notifyElementAdded(element);
        return added;
    }
}
```
```java
public static void main(String[] args) {
    ObservableSet<Integer> set = new ObservableSet<>(new HashSet<>());

    set.addObserver(new SetObserver<>() {
        public void added(ObservableSet<Integer> s, Integer e) {
            System.out.println(e);
            if (e == 23) // 값이 23이면 자신을 구독해지한다.
                s.removeObserver(this);
        }
    });

    for (int i = 0; i < 100; i++)
        set.add(i);
}
```
이 프로그램은 23까지 출력한 다음 `ConcurrentModificationException`을 던진다. <br>
`added` 메서드 호출이 일어난 시점이 `notifyElementAdded`가 관찰자들의 리스트를 순회하는 도중이기 때문이다. <br>

* 쓸데없이 백그라운드 스레드를 사용하는 관찰자
```java
public static void main(String[] args) {
    ObservableSet<Integer> set = new ObservableSet<>(new HashSet<>());

    set.addObserver(new SetObserver<>() {
        public void added(ObservableSet<Integer> s, Integer e) {
            System.out.println(e);
            if (e == 23) {
                ExecutorService exec = Executors.newSingleThreadExecutor();
                try {
                    exec.submit(() -> s.removeObserver(this)).get();
                } catch (ExecutionException | InterruptedException ex) {
                    throw new AssertionError(ex);
                } finally {
                    exec.shutdown();
                }
            }
        }
    });

    for (int i = 0; i < 100; i++)
        set.add(i);
}
```
이 프로그램을 실행하면 예외는 나지 않지만 교착상태에 빠진다.<br>

* 해결책 1 외계인 메서드 호출을 동기화 블록 바깥으로 옮긴다.
```java
private void notifyElementAdded(E element) {
    List<SetObserver<E>> snapshot = null;
    synchronized(observers) {
        snapshot = new ArrayList<>(observers);
    }
    for (SetObserver<E> observer : snapshot)
        observer.added(this, element);
}
```
<br>

* 해결책 2 `CopyOnWriteArrayList`를 사용해 구현한 스레드 안전하고 관찰 가능한 집합
```java
private final List<SetObserver<E>> observers = new CopyOnWriteArrayList<>();
```

<br>

**_기본 규칙은 동기화 영역에서는 가능한 한 일을 적게 하는 것이다._**<br>

* 가변 클래스를 작성하려거든 다음 두 선택지 중 하나를 따르자.
#### 1.동기화를 하지말고, 클래스를 동시에 사용해야 하는 클래스가 외부에서 동기화도록 하자.
#### 2.동기화를 내부에서 수행해 스레드 안전한 클래스로 만들자.
1️⃣ 락 분할(lock splitting) <br>
2️⃣ 락 스트라이이핑(lock striping) <br>
3️⃣ 비차단 동시성 제어(nonblocking concurrency control) <br>

## 🎯  아이템 80. 스레드보다는 실행자, 태스크, 스트림을 애용하라.
`java.util.concurrent` 두둥등장<br>
* 실행자 프레임워크라고 하는 인터페이스 기반의 유연한 태스크 실행 기능을 담고 있음
```java
ExecutorService exec = Executors.newSingleThreadExecutor();
exec.execute(runnable);
exec.shutdown();
```

## 🎯  아이템 81. wait와 notify보다는 동시성 유틸리티를 애용하라.
`wait`와 `notify`는 올바르게 사용하기가 아주 까다로우니 고수준 동시성 유틸리티를 사용하자! <br>

* `java.util.concurrent`의 고수준 유틸리티의 세 범주
  1️⃣ 실행자 프레임워크 <br>
  2️⃣ 동시성 컬렉션 <br>
  3️⃣ 동기화 장치 <br>

1) 실행자 프레임워크는 앞에서 말한 `Executor`로 인터페이스 기반의 유연한 태스트 실행 기능을 담고있다.<br>
2) 동시성 컬렉션은 컬렉션에 동시성을 가미해 구현한 고성능 컬렉션이다.<br>
   **_동시성 컬렉션에서 동시성을 무력화하는 건 불가능하며, 외부에서 락을 추가로 사용하면 오히려 속도가 느려진다._**<br>
3) 동기화 장치는 스레드가 다른 스레드를 기다릴 수 있게 한다.<br>
   예를들어 Queue를 확장한 BlockingQueue도 확장된 기능중 take라는 기능을 볼 수 있는데, <br>
   큐의 첫 번째 원소를 꺼내는 기능으로 만약 큐가 비었다면 새로운 원소가 추가될 떄까지 기다린다. <br>
   그렇기에 BlockingQueue는 작업 큐로 쓰기 적당하고 ThreadPoolExecutor나 실행자 서비스 구현체에서 BlockingQueue를 사용한다.
   그 밖에 `CountDownLatch`,`Semaphore`,`CyclicBarrier`,`Exchanger`,`Phaser` 등이 있다.

## 🎯  아이템 82. 스레드 안전성 수준을 문서화하라.

**_멀티스레드 환경에서도 API를 안전하게 사용하게 하려면 클래스가 지원하는 스레드 안전성 수준을 정확히 명시해야 한다._**<br>
* 스레드 안정성
1) **불변** : 이 클래스의 인스턴스는 마치 상수와 같아서 외부 동기화도 필요 없다. ex) String, Long, BigInteger
2) **무조건적 스레드 안전(unconditionally thread-safe)** : 이 클래스의 인스턴스는 수정될 수 있으나, 내부에서 충실히 <br>
   동기화하여 별도의 외부 동기화 없이 동시에 사용해도 안전하다. ex) AtomicLong, ConcurrentHashMap
3) 조건부 스레드 안전(conditionally thread-safe) : 무조건적 스레드 안전과 같으나, 일부 메서드는 동시에 사용하려면 외부 동기화가 필요하다.<br>
   ex) Collections.synchronized 래퍼 메서드가 반환한 컬렉션들이 여기 속한다.
4) 스레드 안전하지 않음(not thread-safe) : 이 클래스의 인스턴스는 수정될 수 있다. 동시에 사용하려면 각각의 메서드 호출 클라이언트가<br>
   선택한 외부 동기화 매커니즘으로 감싸야 한다. ex) ArrayList, HashMap
5) 스레드 적대적(thread-hostile) : 이 클래스는 모든 메서드 호출을 외부 동기화로 감싸더라도 멀티스레드 환경에서 안전하지 않다.<br>
   이 수준의 클래스는 일반적으로 정적 데이터를 아무 동기화 없이 수정한다. ex) generateSerialNumber

## 🎯  아이템 83. 지연 초기화는 신중히 사용하라.
지연 초기화는 주로 최적화 용도로 쓰이지만, 클래스와 인스턴스 초기화 때 발생하는 위험한 순환 문제를 해결하는 효과도 있다.<br>
하지만, 지연초기화는 양날의 검이다. 클래스 혹은 인스턴스 생성 시의 초기화 비용은 줄지만 그 대신 지연 초기화하는 필드에 접근하는 <br>
비용은 커진다. 지연 초기화하려는 필드들 중 결국 초기화가 이뤄지는 비율에 다라, 실제 초기화에 드는 비용에 따라, 초기화된 각 필드를 <br>
얼마나 빈번히 호출하느냐에 따라 지연 초기화가 실제로는 성능을 느려지게 할 수 있다. <br>

<br>

#### 대부분의 상황에서 일반적인 초기화가 지연 초기화보다 낫다.
지연 초기화가 초기화 순환성을 깨뜨릴 것 같으면 synchronized를 단 접근자를 사용하자.<br>
* 인스턴스 필드의 지연 초기화 - synchronized
```java
private FieldType field;

private synchronized FieldType getField(){
    if(field == null)
        field = computeFieldValue();
    return field;
}
```
**성능 때문에 정적 필드를 지연 초기화해야 한다면 지연 초기화 홀더 클래스 관용구를 사용하자.**<br>
* 정적 필드용 지연 초기화 홀더 클래스 관용구
```java
private static class FieldHolder {
	static final FieldType field = computeFieldValue();
}
private static FieldType getField() {
	return FieldHolder.field;
}
```
**_성능 때문에 인스턴스 필드를 지연 초기화해야 한다면 이중검사 관용구를 사용하라._**<br>
* 인스턴스 필드 지연 초기화용 이중검사 관용구
```java
private volatile FieldType field;

private FieldType getField() {
	FieldType result = field;
	if(result != null) {
		return result;
	}

	synchronized(this) {
		if(field == null) {
			field = computeFieldValue();
		}
		return field;
	}
}
```

<br>

## 🎯  아이템 84. 프로그램의 동작을 스레드 스케줄러에 기대지 말라.
여러 스레드가 실행 중이면 운영체제의 스레드 스케줄러가 어떤 스레드를 얼마나 오래 실행할지 정한다. 정상적으 운영체제라면 <br>
이 작업을 공정하게 수행하지만 구체적인 스케줄링 정책은 운영체제마다 다를 수 있다. 따라서 잘 작성 된 프로그램일라면 이 정책에 <br>
좌지우지돼서는 안된다. **_정확성이나 성능이 스레드 스케줄러에 따라 달라지는 프로그램이라면 다른 플랫폼에 이식하기 어렵다._** <br>

<br>

* 견고하고 빠릿하고 이식성 좋은 프로그램을 작성하는 가장 좋은 방법
1) 실행 가능한 스레드의 평균적인 수를 프로세서 수보다 지나치게 많아지지 않도록 하는 것. <br>
   그래야 스레드 스케줄러가 고민할 거리가 줄어든다. <br>
2) **_스레드는 당장 처리해야 할 작업이 없다면 실행돼서는 안 된다._** <br>
   스레드 풀 크기를 적절히 설정하고 작업은 짧게 유지하면 된다. 단, 너무 짧으면 작업을 분배하는 부담이 오히려 성능을 떨어뜨릴 수 있다.<br>
3) 스레드는 절대 바쁜 대기(busy waiting) 상태가 되면 안된다.<br>
   공유 객체의 상태가 바뀔 때가지 쉬지 않고 검사해서는 안된다는 뜻이다. 바쁜 대기는 스레드 스케줄러의 변덕에 취약할 뿐 아니라, <br>
   프로세서에 큰 부담을 주어 다른 유용한 작업이 실행될 기회를 박탈한다.<br>
```java
public class SlowCountDownLatch {
    private int count;

    public SlowCountDownLatch(int count) {
        if (count < 0)
            throw new IllegalArgumentException(count + " < 0");
        this.count = count;
    }

    public void await() {
        while (true) {
            synchronized(this) {
                if (count == 0)
                    return;
            }
        }
    }
    public synchronized void countDown() {
        if (count != 0)
            count--;
    }
}
```

4) **Thread.yield를 써서 문제를 고쳐보려는 유혹을 떨쳐내자.**<br>
   특정 스레드에서 다른 스레드보다 CPU 시간을 충분히 얻지 못한다고 Thread.yield를 사용하고자 하는 마음을 버려야 한다. <br>
   이식성이 좋지 않고  테스트할 수단도 없고 오히려 느려질 가능성도 있다. 차라리 위에서 언급했던대로 실행 가능한 스레드 수를 줄여보도록 하자. <br>
   Thread.yield나 스레드 우선순위같은 기능들은 스레드 스케줄러에게 제공하는 힌트로 이미 잘 돌아가는 서비스의 품질을 높히기 위해 드물게 <br>
   사용될 뿐 수리의 목적으로 사용해서는 안된다. <br>

<br>

> Joshua Bloch, 『Effective Java 3/E』, 개앞맵시 옮김, 프로그래밍인사이트(2018), p413-448.