# ๐ฅ [ Chapter3 ] ๋ชจ๋  ๊ฐ์ฒด์ ๊ณตํต ๋ฉ์๋

## ๐ฏ  ์์ดํ 10. equals๋ ์ผ๋ฐ ๊ท์ฝ์ ์ง์ผ ์ฌ์ ์ํ๋ผ.
## ๐ฏ  ์์ดํ 11. equals๋ฅผ ์ฌ์ ์ํ๋ ค๊ฑฐ๋  hashCode๋ ์ฌ์ ์ํ๋ผ.
```java
@EqualsAndHashCode
public class Admin {
    ...
}
```
> https://projectlombok.org/features/EqualsAndHashCode


## ๐ฏ  ์์ดํ 12. toString์ ํญ์ ์ฌ์ ์ํ๋ผ.
```java
@Override
public String toString() {
    return "User{" +
            "userId='" + userId + '\'' +
            ", userPw='" + userPw + '\'' +
            ", userNm='" + userNm + '\'' +
            '}';
}
```

## ๐ฏ  ์์ดํ 13. clone ์ฌ์ ์๋ ์ฃผ์ํด์ ์งํํ๋ผ.

```java
@Override
public Item13 clone() {
    try {
        return (Item13) super.clone();
    } catch (CloneNotSupportedException e) {
        throw new RuntimeException();
    }
}
```

## ๐ฏ  ์์ดํ 14. Comparable์ ๊ตฌํํ ์ง ๊ณ ๋ คํ๋ผ.

```java
public class Item14 implements Comparable<Item14> {

    private Integer integer;

    public Item14(Integer integer) {
        this.integer = integer;
    }

    @Override
    public int compareTo(Item14 o) {
        return Integer.compare(integer, o.integer);
    }

    public static void main(String[] args) {
        Item14 item = new Item14(3);
        Item14 item2 = new Item14(1);
        Item14 item3 = new Item14(5);
        Item14 item4 = new Item14(3);
        System.out.println(item.compareTo(item2)); //  1
        System.out.println(item.compareTo(item3)); // -1
        System.out.println(item.compareTo(item4)); //  0
    }
}
```
