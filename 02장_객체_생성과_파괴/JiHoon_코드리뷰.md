# 2장. 객체 생성과 파괴

## 아이템 1. 생성자 대신 정적 팩터리 메서드를 고려하라.

#### ⛔️ 이전 코드
```java

private String color; // 카드 색상
private int paymentLimit; // 결제한도

// 결제 한도에 따른 색상이 다르다. 
// 파라미터가 완전히 동일한 시그니처가 2개 이상 존재 할 수 없다.
public Card(int paymentLimit) {
    this.paymentLimit = paymentLimit;
    this.color = "red";
}

public Card(int paymentLimit) {
    this.paymentLimit = paymentLimit;
    this.color = "blue";
}
```

#### ✅ 변경 후 코드
```java
// 시그니처가 동일한 경우 정적 팩토리 메소드를 고려하라
public static Card createBlueCard(int paymentLimit) { // 객체의 특성을 고려한 이름
    Card card = new Card();
    card.color = "blue";
    card.paymentLimit = paymentLimit;
    return card;
}

public static Card createGreenCard(int paymentLimit) {
    Card card = new Card();
    card.color = "green";
    card.paymentLimit = paymentLimit;
    return card;
}
```

#### 📋 코드리뷰 정리
- 객체의 특성을 훨씬 잘 표현할 수 있다.
- 같은 파라미터를 가진 시그니처를 생성할 때 사용할 수 있다.

## 아이템 2. 생성자에 매개변수가 많다면 빌더를 고려하라.
> 편의상 빌더 패턴과 매개변수들은 작성이 돼 있다는 전제하에 코드 작성했습니다.

#### ⛔️ 이전 코드
```java
public static void main(String[] args) {
    // 이 코드는 1회 제공량이 150일까 아니면 제공 횟수가 150인걸까?
    NutritionFacts nutritionFacts1 = new NutritionFacts(150, 2, 0);
    // 이 코드는 나트륨 제공량이 몇 일까? 그리고 0인 값을 꼭 적어줘야 하는 것일까
    NutritionFacts nutritionFacts2 = new NutritionFacts(150, 2, 100, 0, 12, 10);
}
```

#### ✅ 변경 후 코드
```java
public static void main(String[] args) {
    NutritionFacts burger = new NutritionFacts.Builder(500, 1)
            .calories(200)
            .sodium(35)
            .carbohydrate(15)
            .build();
}
```
#### 📋 코드리뷰 정리
- 이전 코드에 비해 훨씬 보기 좋아졌으며, 어떤 값이 어느 변수에 해당하는지 확실하게 알 수 있다.

## 아이템 3. private 생성자나 열거 타입으로 싱글턴임을 보증하라

#### ⛔️ 이전 코드
```java
public class SingletonTest {
    private static final SingletonTest INSTANCE = new SingletonTest2();

    private SingletonTest() {}

    public static SingletonTest getInstance() {
        return INSTANCE;
    }
}
```

#### ✅ 변경 후 코드
```java
public enum SingletonTest {
	INSTANCE; 
	
	public String getName() {
		return "SingletonTest";
	}

	public void leaveTheBuilding() { ... }
}
```

#### 📋 코드리뷰 정리
- 원소가 하나뿐인 상황이므로 enum타입으로 만들어주면 역직렬화 상황이나 리플렉션 공격에도 제2의 인스턴스가 생기는 일을 막아준다.

## 아이템 4. 인스턴스화를 막으려거든 private 생성자를 사용하라.

#### ⛔️ 이전 코드
```java
// 인스턴화를 막기 위해서 추상화를 한다면 그것은 정답이 아니다.
public abstract class NoInstance {
    public static void print(String message) {
        System.out.println(message);
    }
    
    public class Instance extends NoInstance{
        public Instance(){
            super();
        }
    }
}
```

#### ✅ 변경 후 코드
```java
public class NoInstance {
    private NoInstance() {
        // 꼭 필요는 없지만 실수로라도 생성자를 호출하지 않도록 도와준다.
        // 명시적으로 적어주기
        throw new AssertionError();
    }
    
    public static void print(String message) {
        System.out.println(message);
    }
}
```

#### 📋 코드리뷰 정리
인스턴스화를 안막아도 사용하는데는 문제는 없습니다.  
다만 그 자체로 사용을 하면 컴파일러가 기본 생성자를 생성하게 됩니다.
즉 정적 필드와 정적 메서드 밖에 없는데 인스턴스를 생성하게 되는 것이기 때문에 막아두는 편이 좋다.


## 아이템 5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라.

#### ⛔️ 이전 코드
```java
// 싱글턴을 잘못 사용한 예 - 유연하지 않고 테스트하기 어렵다
public class SpellChecker{
  private static final Lexicon dictionary = ...; // 사전에 의존한다.
  
  private SpellChecker(...) {} 
  public static SpellChecker INSTANCE = new SpellChecker(...);

  public static boolean isValid(String word){...}
  public static List<String> suggestions(String typo){...}
}
```

#### ✅ 변경 후 코드
```java
// 의존 객체 주입은 유연성과 테스트 용이성을 높여준다 
public class SpellChecker{
  private final Lexicon dictionary;
  
  public SpellChecker(Lexicon dictionary){ // 의존 객체 주입
    this.dictionay = Objects.requiredNonNull(dictionay);
  }
  
  public boolean isValid(String word){...}
  public List<String> suggestions(String typo){...}
}
```

#### 📋 코드리뷰 정리
- 이전코드는 테스트코드 작성이 어려웠던 반면 변경후에는 테스트 코드 작성하는데 무리가 없다.
- 불변을 보장할 수 있다.

## 아이템 6. 불필요한 객체 생성을 피하라.

#### ⛔️ 이전 코드
```java
@Test
@DisplayName("오토박싱 테스트")
public void 오토박싱() {
    Long sum = 0L;
    for (long i = 0; i <= Integer.MAX_VALUE; i++) sum += i; // 오토박싱 발생
}
```
![image](https://user-images.githubusercontent.com/53300830/163193780-a323cab6-6d15-4598-baa6-2f697abf81b4.png)
#### ✅ 변경 후 코드
```java
@Test
@DisplayName("오토박싱 해결 테스트")
public void 오토박싱해결() {
    long sum = 0L; // Long -> long 변경
    for (long i = 0L; i <= Integer.MAX_VALUE; i++) sum += i; // long 타입 통일로 인해 오토박싱 해결
}
```
![image](https://user-images.githubusercontent.com/53300830/163193733-5e0a4840-7755-41ac-9f81-dec9b760adcd.png)
#### 📋 코드리뷰 정리
- 오토박싱이나 언박싱을 할경우 성능 저하가 엄청 심하다는 것을 알 수 있었다.
- 타입을 프리티머브 타입으로 바꾸면서 성능이 확실히 개선 된 모습을 볼 수 있다.

# 2장. 객체 생성과 파괴

## 아이템 7. 다 쓴 객체 참조를 해제하라.

#### ⛔️ 이전 코드
```java
public static void main(String[] args) {
    HashMap<Integer, String> map = new HashMap<>();
    Integer key1 = 1000;
    Integer key2 = 2000;

    map.put(key1, "test a");
    map.put(key2, "test b");

    key1 = null; // null 처리해줬지만 HashMap에서는 동기화가 되지 않는다.

    System.gc();  // 강제 Garbage Collection 호출

    map.entrySet().forEach(el -> System.out.println(el));
}
```

![image](https://user-images.githubusercontent.com/53300830/163341874-73c633e1-46fc-4806-98d9-de0a8527c3d0.png)
> key1.value 및 size 전부 살아있는 모습을 볼 수 있다.  
> 살아있는 캐시로 인해 메모리 누수가 발생중인 모습이다.

#### ✅ 변경 후 코드
```java
public static void main(String[] args) {
    WeakHashMap<Integer, String> map = new WeakHashMap<>();
    Integer key1 = 1000;
    Integer key2 = 2000;

    map.put(key1, "test a");
    map.put(key2, "test b");

    key1 = null; // (key1,"test a") 자동으로 삭제

    System.gc();  // 강제 Garbage Collection 호출

    map.entrySet().forEach(el -> System.out.println(el));
}
```
![image](https://user-images.githubusercontent.com/53300830/163341624-43fd86ad-bc95-463d-8ba3-df88a6ad0d2d.png)
> key1과 key1.value 값이 사라진 것을 볼 수 있다.

#### 📋 코드리뷰 정리
- 메모리 누수를 생각하면 WeakHashMap을 사용하는 것을 고려해봐야한다.

## 아이템 8. finalizer와 cleaner 사용을 피하라

#### ✏️ 공통 코드
```java
public class Room implements AutoCloseable{
    private static final Cleaner cleaner = Cleaner.create();

    // Room을 참조해서는 안된다.
    // Room을 참조하게 되면 순환 참조가 일어나게 된다.
    private static class State implements Runnable {
        int numJunkPiles; // 수거할 자원

        public State(final int numJunkPiles) {
            this.numJunkPiles = numJunkPiles; // 수거대라고 생각
        }
        /**
         * 1. close() 를 호출 할 때
         * 2. cleaner(안전망)
         */
        @Override
        public void run() {
            System.out.println("방 청소 진행 중");
            numJunkPiles = 0;
        }
    }

    private final State state;

    private final Cleaner.Cleanable cleanable;

    public Room(final int numJunkFiles) {
        this.state = new State(numJunkFiles);
        cleanable = cleaner.register(this, state); // Runnable 객체를 등록
    }

    @Override
    public void close() throws Exception {
        cleanable.clean();
    }
}
```

#### ⛔️ 이전 코드
```java
@Test
@DisplayName("방 청소 테스트 언제 할거야?")
void notClean() throws Exception {
    Room room = new Room(7);
    // "방 청소 진행 중"은 절대로 출력되지 않는다.
    // 직접 close를 호출해야만 한다.
    // room.close(); 
    System.out.println("제발 청소 좀 해");
}
```
![image](https://user-images.githubusercontent.com/53300830/163536949-d3d88d40-4efc-483d-8621-199d630fcfad.png)

#### ✅ 변경 후 코드
```java
@Test
@DisplayName("방 청소 테스트")
void autoClean() throws Exception {
    // 잘 짜여진 클라이언트 코드 예시 (try-with-resources)
    try (Room myRoom = new Room(7)) {
        System.out.println("청소 시작");
    } finally {
        System.out.println("청소 완료");
    }
}
```
![image](https://user-images.githubusercontent.com/53300830/163539608-7a9012b9-1d22-4709-950b-4758c868f572.png)

#### 📋 코드리뷰 정리
- try-finally로 바꾸자 정상적으로 호출하는 모습을 볼 수 있다.
- 그 반면 이전 코드는 close()를 호출하지 않고 내가 원하는 때에 실행이 되지 않는다.


## 아이템9. try-finally 보다는 try-with-resources를 사용하라.

#### ⛔️ 이전 코드
```java
// 9-2 try-finally 방식
// 자원이 둘 이상이면 너무 지저분해진다.
// in.read에서 문제가 생길수 있고, close() 할때도 문제가 생길수 있다.
static void tryFinally(String src, String dst) throws IOException {
    InputStream in = new FileInputStream(src);
    try {
        OutputStream out = new FileOutputStream(dst);
        try {
            byte[] buf = new byte[BUFFER_SIZE];
            int n;
            while ((n = in.read(buf)) >= 0)
                out.write(buf, 0, n);
        } finally {
            out.close();
        }
    } finally {
        in.close();
    }
}
```

#### ✅ 변경 후 코드
```java
// 9-4. try-with-resources 방식
// 짧고 읽기 쉬우며, 문제를 진단하기도 훨씬 좋다.
static void tryWithResources(String src, String dst) throws IOException {
    try (InputStream in = new FileInputStream(src);
         OutputStream out = new FileOutputStream(dst)) {
        byte[] buf = new byte[BUFFER_SIZE];
        int n;
        while ((n = in.read(buf)) >= 0)
            out.write(buf, 0, n);
    }
}

```

#### 📋 코드리뷰 정리
- 디버깅이 힘들다는 단점이 보완된다. 
- 코드의 가독성 향상이 된다.
- `try-finally` 말고 `try-with-resources`를 사용하는 것으로 통일하는 것이 좋다.
