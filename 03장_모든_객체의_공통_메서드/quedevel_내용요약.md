# ๐ฅ [ Chapter3 ] ๋ชจ๋  ๊ฐ์ฒด์ ๊ณตํต ๋ฉ์๋
_<span style='font-weight: bold;'>`Object`</span>๋ ๊ฐ์ฒด๋ฅผ ๋ง๋ค ์ ์๋ ๊ตฌ์ฒด ํด๋์ค์ง๋ง ๊ธฐ๋ณธ์ ์ผ๋ก๋ ์์ํด์ ์ฌ์ฉํ๋๋ก ์ค๊ณ๋์๋ค.<br>_
_<span style='font-weight: bold;'>`Object`</span>์์ `final`์ด ์๋ ๋ฉ์๋๋ ๋ชจ๋ ์ฌ์ ์๋ฅผ ์ผ๋์ ๋๊ณ  ์ค๊ณ๋ ๊ฒ์ด๋ผ ์ฌ์ ์ ์ ์ง์ผ์ผ ํ๋<br>_
_์ผ๋ฐ ๊ท์ฝ์ด ๋ชํํ ์ ์๋์ด ์๋ค._

## ๐ฏ  ์์ดํ 10. equals๋ ์ผ๋ฐ ๊ท์ฝ์ ์ง์ผ ์ฌ์ ์ํ๋ผ.
+ **`Object` ๋ช์ธ์ ์ ํ ๊ท์ฝ โ**
> 1๏ธโฃ ๋ฐ์ฌ์ฑ : null์ด ์๋ ๋ชจ๋  ์ฐธ์กฐ ๊ฐ x์ ๋ํด, x.equals(x)๋ true๋ค.<br>
> 2๏ธโฃ ๋์นญ์ฑ : null์ด ์๋ ๋ชจ๋  ์ฐธ์กฐ ๊ฐ x,y์ ๋ํด, x.equals(y)๊ฐ true๋ฉด y.equals(x)๋ true๋ค.<br>
> 3๏ธโฃ ์ถ์ด์ฑ : null์ด ์๋ ๋ชจ๋  ์ฐธ์กฐ ๊ฐ x,y,z์ ๋ํด, x.equals(y)๊ฐ true์ด๊ณ  y.equals(z)๋ true๋ฉด x.equals(z)๋ true๋ค.<br>
> 4๏ธโฃ ์ผ๊ด์ฑ : null์ด ์๋ ๋ชจ๋  ์ฐธ์กฐ ๊ฐ x,y์ ๋ํด, x.equals(y)๋ฅผ ๋ฐ๋ณตํด์ ํธ์ถํ๋ฉด ํญ์ true๋ฅผ ๋ฐํํ๊ฑฐ๋ ํญ์ false๋ฅผ ๋ฐํํ๋ค.<br>
> 5๏ธโฃ null-์๋ : null์ด ์๋ ๋ชจ๋  ์ฐธ์กฐ ๊ฐ x์ ๋ํด, x.equals(null)์ false๋ค.<br>

 <br>

+ **์๋ชป๋ ์ฝ๋ - ๋์นญ์ฑ ์๋ฐฐ โ**
```java
public final class CaseInsensitiveString {
    private final String s;
    
    public CaseInsensitiveString(String s){
        this.s = Object.requireNonNull(s);
    }
    
    // ๋์นญ์ฑ ์๋ฐฐ !!
    @Override
    public boolean equals(Object o){
        if(o instanceof CaseInsensitiveString) return s.equalsIgnoreCase(((CaseInsensitiveString) o).s);
        if(o instanceof String) return s.equalsIgnoreCase(((String) o));
        return false;
    }
    ... 
}
```
โ ๏ธ`CaseInsensitiveString`์ `equals`๋ ์ผ๋ฐ`String`์ ์๊ณ ์์ง๋ง `String`์ `equals`๋ `CaseInsensitiveString`์ ์กด์ฌ๋ฅผ ๋ชจ๋ฅธ๋ค๋๋ฐ ์๋ค.<br>
โ ๏ธ `equals` ๊ท์ฝ์ ์ด๊ธฐ๋ฉด ๊ทธ ๊ฐ์ฒด๋ฅผ ์ฌ์ฉํ๋ ๋ค๋ฅธ ๊ฐ์ฒด๋ค์ด ์ด๋ป๊ฒ ๋ฐ์ํ ์ง ์ ์ ์๋ค.<br>
โ ๏ธ ๊ตฌ์ฒด ํด๋์ค๋ฅผ ํ์ฅํด ์๋ก์ด ๊ฐ์ ์ถ๊ฐํ๋ฉด์ `equals` ๊ท์ฝ์ ๋ง์กฑ์ํฌ ๋ฐฉ๋ฒ์ ์กด์ฌํ์ง ์๋๋ค.<br><br><br>

+ **์์ง์ `equals` ๋ฉ์๋ ๊ตฌํ ๋ฐฉ๋ฒ์ ๋จ๊ณ๋ณ๋ก ์ ๋ฆฌ โ**
> 1๏ธโฃ == ์ฐ์ฐ์๋ฅผ ์ฌ์ฉํด ์๋ ฅ์ด ์๊ธฐ ์์ ์ ์ฐธ์กฐ์ธ์ง ํ์ธํ๋ค.<br>
> 2๏ธโฃ instanceof ์ฐ์ฐ์๋ก ์๋ ฅ์ด ์ฌ๋ฐ๋ฅธ ํ์์ธ์ง ํ์ธํ๋ค.<br>
> 3๏ธโฃ ์๋ ฅ์ ์ฌ๋ฐ๋ฅธ ํ์์ผ๋ก ํ๋ณํํ๋ค.<br>
> 4๏ธโฃ ์๋ ฅ ๊ฐ์ฒด์ ์๊ธฐ ์์ ์ ๋์๋๋ 'ํต์ฌ'ํ๋๋ค์ด ๋ชจ๋ ์ผ์นํ๋์ง ํ๋์ฉ ๊ฒ์ฌํ๋ค.<br>


## ๐ฏ  ์์ดํ 11. equals๋ฅผ ์ฌ์ ์ํ๋ ค๊ฑฐ๋  hashCode๋ ์ฌ์ ์ํ๋ผ.
+ **`Object` ๋ช์ธ์ ์ ํ ๊ท์ฝ โ**
> 1๏ธโฃ equals ๋น๊ต์ ์ฌ์ฉ๋๋ ์ ๋ณด๊ฐ ๋ณ๊ฒฝ๋์ง ์์๋ค๋ฉด, ์ ํ๋ฆฌ์ผ์ด์์ด ์คํ๋๋ ๋์ ๊ทธ ๊ฐ์ฒด์ hashCode ๋ฉ์๋๋ <br>&nbsp; &nbsp; &nbsp; ๋ช ๋ฒ์ ํธ์ถํด๋ ์ผ๊ด๋๊ฒ ํญ์ ๊ฐ์ ๊ฐ์ ๋ฐํํด์ผ ํ๋ค.<br>
> 2๏ธโฃ equals(Object)๊ฐ ๋ ๊ฐ์ฒด๋ฅผ ๊ฐ๋ค๊ณ  ํ๋จํ๋ค๋ฉด, ๋ ๊ฐ์ฒด์ hashCode๋ ๋๊ฐ์ ๊ฐ์ ๋ฐํํด์ผ ํ๋ค.<br>
> 3๏ธโฃ equals(Object)๊ฐ ๋ ๊ฐ์ฒด๋ฅผ ๋ค๋ฅด๋ค๊ณ  ํ๋จํ๋๋ผ๋, ๋ ๊ฐ์ฒด์ hashCode๊ฐ ์๋ก ๋ค๋ฅธ ๊ฐ์ ๋ฐํํ  ํ์๋ ์๋ค.<br>

**โ ๏ธ`hashCode` ์ฌ์ ์๋ฅผ ์๋ชปํ์ ๋ ํฌ๊ฒ ๋ฌธ์ ๊ฐ ๋๋ ์กฐํญ์ ๋ ๋ฒ์ฌ๋ค. ์ฆ, ๋ผ๋ฆฌ์ ์ผ๋ก ๊ฐ์ ๊ฐ์ฒด๋ ๊ฐ์ ํด์์ฝ๋๋ฅผ ๋ฐํํด์ผ ํ๋ค.**


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
+ `toString`์ ๊ทธ ๊ฐ์ฒด๊ฐ ๊ฐ์ง ์ฃผ์ ์ ๋ณด ๋ชจ๋๋ฅผ ๋ฐํํ๋๊ฒ ์ข๋ค.
+ `toString`์ด ๋ฐํํ ๊ฐ์ ํฌํจ๋ ์ ๋ณด๋ฅผ ์ป์ด์ฌ ์ ์๋ API๋ฅผ ์ ๊ณตํ์.

## ๐ฏ  ์์ดํ 13. clone ์ฌ์ ์๋ ์ฃผ์ํด์ ์งํํ๋ผ.
**`Cloneable`์ ๋ณต์ ํด๋ ๋๋ ํด๋์ค์์ ๋ช์ํ๋ ์ฉ๋์ <span style='color: red;'>๋ฏน์ค์ธ ์ธํฐํ์ด์ค</span>์ง๋ง, ์์ฝ๊ฒ๋ ์๋ํ ๋ชฉ์ ์ ์ ๋๋ก ์ด๋ฃจ์ง ๋ชปํ๋ค.**

**๊ฐ์ฅ ํฐ ๋ฌธ์ ๋ `clone` ๋ฉ์๋๊ฐ ์ ์ธ๋ ๊ณณ์ด `Cloneable`์ด ์๋ `Object`์ด๊ณ , ๊ทธ๋ง์ ๋ <span style='color: red;'>protected</span>๋ผ๋ ๋ฐ ์๋ค.**
```java
protected native Object clone() throws CloneNotSupportedException;
```
+ **`Object` ๋ช์ธ์ ์ ํ ๊ท์ฝ โ**
> ์ด ๊ฐ์ฒด์ ๋ณต์ฌ๋ณธ์ ์์ฑํด ๋ฐํํ๋ค. '๋ณต์ฌ'์ ์ ํํ ๋ป์ ๊ทธ ๊ฐ์ฒด๋ฅผ ๊ตฌํํ ํด๋์ค์ ๋ฐ๋ผ ๋ค๋ฅผ ์ ์๋ค.<br>
> ์ผ๋ฐ์ ์ธ ์๋๋ ๋ค์๊ณผ ๊ฐ๋ค. ์ด๋ค ๊ฐ์ฒด x์ ๋ํด ๋ค์ ์์ ์ฐธ์ด๋ค.<br><br>
> _x.clone() != x_<br><br>
> ๋ํ ๋ค์ ์๋ ์ฐธ์ด๋ค.<br><br>
> _x.clone().getClass()_ == x.getClass()<br><br>
> ํ์ง๋ง ์ด์์ ์๊ตฌ๋ฅผ ๋ฐ๋์ ๋ง์กฑํด์ผ ํ๋ ๊ฒ์ ์๋๋ค.<br>
> ํํธ ๋ค์ ์๋ ์ผ๋ฐ์ ์ผ๋ก ์ฐธ์ด์ง๋ง, ์ญ์ ํ์๋ ์๋๋ค.<br><br>
> _x.clone().equals(x)_<br><br>
> ๊ด๋ก์, ์ด ๋ฉ์๋๊ฐ ๋ฐํํ๋ ๊ฐ์ฒด๋ super.clone์ ํธ์ถํด ์ป์ด์ผ ํ๋ค.<br>
> ์ด ํด๋์ค(Object๋ฅผ ์ ์ธํ) ๋ชจ๋  ์์ ํด๋์ค๊ฐ ์ด ๊ด๋ก๋ฅผ ๋ฐ๋ฅธ๋ค๋ฉด ๋ค์ ์์ ์ฐธ์ด๋ค.<br><br>
> _x.clone().getClass() == x.getClass()_<br><br>
> ๊ด๋ก์, ๋ฐํ๋ ๊ฐ์ฒด์ ์๋ณธ ๊ฐ์ฒด๋ ๋๋ฆฝ์ ์ด์ด์ผ ํ๋ค.<br>
> ์ด๋ฅผ ๋ง์กฑํ๋ ค๋ฉด super.clone์ผ๋ก ์ป์ ๊ฐ์ฒด์ ํ๋ ์ค ํ๋ ์ด์์ ๋ฐํ ์ ์ ์์ ํด์ผ ํ  ์๋ ์๋ค.<br>


<br>

* ๊ฐ๋ณ ์ํ๋ฅผ ์ฐธ์กฐํ์ง ์๋ ํด๋์ค์ฉ `clone` ๋ฉ์๋
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
์ด ๋ฉ์๋๊ฐ ๋์ํ๊ฒ ํ๋ ค๋ฉด Item13์ ํด๋์ค ์ ์ธ์ `Cloneable`์ ๊ตฌํํด์ผํ๋ค.<br>
๋ง์ฝ ๊ตฌํํ์ง ์๋๋ค๋ฉด `CloneNotSupportedException`์ ๋์ง๊ฒ ๋๋ค.<br>
`clone` ๋ฉ์๋๋ ์ฌ์ค์ ์์ฑ์์ ๊ฐ์ ํจ๊ณผ๋ฅผ ๋ธ๋ค.<br>
์ฆ, `clone`์ ์๋ณธ ๊ฐ์ฒด์ ์๋ฌด๋ฐ ํด๋ฅผ ๋ผ์น์ง ์๋ ๋์์ ๋ณต์ ๋ ๊ฐ์ฒด์ ๋ถ๋ณ์์ ๋ณด์ฅํด์ผํ๋ค.<br>
๋ฐ๋ผ์, ๊ธฐ๋ณธ ์์น์ '๋ณต์  ๊ธฐ๋ฅ์ ์์ฑ์์ ํฉํฐ๋ฆฌ๋ฅผ ์ด์ฉํ๋๊ฒ ์ต๊ณ '๋ผ๋ ๊ฒ์ด๋ค.<br>
๋จ, ๋ฐฐ์ด๋ง์ `clone` ๋ฉ์๋ ๋ฐฉ์์ด ๊ฐ์ฅ ๊น๋ํ, ์ด ๊ท์น์ ํฉ๋นํ ์์ธ๋ผ ํ  ์ ์๋ค.<br>
(๋ฐฐ์ด์ ๊ฐ elements๋ ์ฌ๊ท์ ์ผ๋ก `clone`์ ํด์ผํ๊ธฐ ๋๋ฌธ์ด๋ค.)


## ๐ฏ  ์์ดํ 14. Comparable์ ๊ตฌํํ ์ง ๊ณ ๋ คํ๋ผ.
_**์ํ๋ฒณ, ์ซ์, ์ฐ๋ ๊ฐ์ด ์์๊ฐ ๋ชํํ ๊ฐ ํด๋์ค๋ฅผ ์์ฑํ๋ค๋ฉด ๋ฐ๋์ `Comparable` ์ธํฐํ์ด์ค๋ฅผ ๊ตฌํํ์.**_
> ์ด ๊ฐ์ฒด์ ์ฃผ์ด์ง ๊ฐ์ฒด์ ์์๋ฅผ ๋น๊ตํ๋ค. ์ด ๊ฐ์ฒด๊ฐ ์ฃผ์ด์ง ๊ฐ์ฒด๋ณด๋ค ์์ผ๋ฉด ์์ ์ ์๋ฅผ,<br>
> ๊ฐ์ผ๋ฉด 0์, ํฌ๋ฉด ์์ ์ ์๋ฅผ ๋ฐํํ๋ค. ์ด ๊ฐ์ฒด์ ๋น๊ตํ  ์ ์๋ ํ์์ ๊ฐ์ฒด๊ฐ ์ฃผ์ด์ง๋ฉด<br>
> `ClassCastException` ์ ๋์ง๋ค.

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
        System.out.println(item.compareTo(item2)); // 1
        System.out.println(item.compareTo(item3)); // -1
        System.out.println(item.compareTo(item4)); // 0
    }
}
```
## โญ ๊ฒฐ๋ก 
**_Object์์ final์ด ์๋ ๋ฉ์๋๋ ๋ชจ๋ ์ฌ์ ์๋ฅผ ์ผ๋์ ๋๊ณ  ์ค๊ณ๊ฐ ๋์์ง๋ง...<br>
๋ฐ๋์ ๊ฐ๊ฐ์ ๊ท์ฝ ๋ฐ๋ผ์ ์ฌ์ ์ ํ  ์ ์๋๋กํ๋ค._**


<br>

> Joshua Bloch, ใEffective Java 3/Eใ, ๊ฐ์๋งต์ ์ฎ๊น, ํ๋ก๊ทธ๋๋ฐ์ธ์ฌ์ดํธ(2018), p51-94.