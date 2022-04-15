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

#### 이전 코드
```java

```
<br>

#### 변경 후 코드
```java

```
<br>

#### 코드리뷰 정리

## 아이템9. try-finally 보다는 try-with-resources를 사용하라.
<br>

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
<br>

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
<br>

#### 코드리뷰 정리
- 디버깅이 힘들다는 단점이 보완된다. 
- 코드의 가독성 향상
