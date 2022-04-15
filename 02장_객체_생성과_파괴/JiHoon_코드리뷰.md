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

#### 이전 코드
```java

```
<br>

#### 변경 후 코드
```java

```
<br>

#### 코드리뷰 정리

## 아이템 6. 불필요한 객체 생성을 피하라.
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

# 2장. 객체 생성과 파괴

## 아이템 7. 다 쓴 객체 참조를 해제하라.
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

## 아이템 8. finalizer와 cleaner 사용을 피하라
<br>

#### 공통 코드
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

#### 이전 코드
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

#### 변경 후 코드
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

#### 코드리뷰 정리
- try-finally로 바꾸자 정상적으로 호출하는 모습을 볼 수 있다.
- 그 반면 이전 코드는 close()를 호출하지 않고 내가 원하는 때에 실행이 되지 않는다.


## 아이템9. try-finally 보다는 try-with-resources를 사용하라.

#### 이전 코드
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

#### 변경 후 코드
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

#### 코드리뷰 정리
- 디버깅이 힘들다는 단점이 보완된다. 
- 코드의 가독성 향상이 된다.
- `try-finally` 말고 `try-with-resources`를 사용하는 것으로 통일하는 것이 좋다.
