# 🔥 [ Chapter3 ] 모든 객체의 공통 메서드

## 🎯  아이템 10. equals는 일반 규약을 지켜 재정의하라.
## 🎯  아이템 11. equals를 재정의하려거든 hashCode도 재정의하라.
```java
@EqualsAndHashCode
public class Admin {
    ...
}
```
> https://projectlombok.org/features/EqualsAndHashCode


## 🎯  아이템 12. toString을 항상 재정의하라.
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

## 🎯  아이템 13. clone 재정의는 주의해서 진행하라.

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

## 🎯  아이템 14. Comparable을 구혈할지 고려하라.

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
