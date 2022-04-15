# 2장. 객체 생성과 파괴

## 아이템 1. 생성자 대신 정적 팩터리 메서드를 고려하라.
<br>

#### 이전 코드
```java

```
<br>

#### 변경 후 코드
```java

```
<br>

#### 코드리뷰 정리

## 아이템 2. 생성자에 매개변수가 많다면 빌더를 고려하라.
<br>

#### 이전 코드
```java

```
<br>

#### 변경 후 코드
```java

```
<br>

#### 코드리뷰 정리

## 아이템 3. private 생성자나 열거 타입으로 싱글턴임을 보증하라
<br>

#### 이전 코드
```java

```
<br>

#### 변경 후 코드
```java

```
<br>

#### 코드리뷰 정리

## 아이템 4. 인스턴스화를 막으려거든 private 생성자를 사용하라.
<br>

#### 이전 코드
```java

```
<br>

#### 변경 후 코드
```java

```
<br>

#### 코드리뷰 정리

## 아이템 5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라.
<br>

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
<br>

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
<br>

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
