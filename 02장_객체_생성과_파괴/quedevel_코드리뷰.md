# 2장. 객체 생성과 파괴

## 아이템1. 생성자 대신 정적 팩터리 메서드를 고려하라.

### Item1
```java
public class Item1 {

    static class Member {
        private String mbrId;
        private String mbrPw;

        public static Member ofMemberIdAndMbrPw(String mbrId, String mbrPw){
            Member member = new Member();
            member.setMbrId(mbrId);
            member.setMbrPw(mbrPw);
            return member;
        }

        public String getMbrId() {
            return mbrId;
        }
        public Member setMbrId(String mbrId) {
            this.mbrId = mbrId;
            return this;
        }
        public String getMbrPw() {
            return mbrPw;
        }
        public Member setMbrPw(String mbrPw) {
            this.mbrPw = mbrPw;
            return this;
        }
    }

    public static void main(String[] args) {

        Member m = Member.ofMemberIdAndMbrPw("quedevel", "1234");
        System.out.println("m.getMbrId() = " + m.getMbrId());

    }
}

```


## 아이템2. 생성자에 매개변수가 많다면 빌더를 고려하라.

### Item2
```java
public class Item2 {

    static class Member{
        private long mbrSn;
        private String mbrId;
        private String mbrPw;
        private String mbrNm;
        private String email;
        private String gender;
        private int age;
        private int hp1;
        private int hp2;
        private int hp3;

        public Member(Builder builder){
            this.mbrSn = builder.mbrSn;
            this.mbrId = builder.mbrId;
            this.mbrPw = builder.mbrPw;
            this.mbrNm = builder.mbrNm;
            this.email = builder.email;
            this.gender = builder.gender;
            this.age = builder.age;
            this.hp1 = builder.hp1;
            this.hp2 = builder.hp2;
            this.hp3 = builder.hp3;
        }

        public static Builder builder(String id, String pw){
            return new Builder(id, pw);
        }

        public static class Builder {
            public long mbrSn;
            public String mbrId;
            public String mbrPw;
            public String mbrNm;
            public String email;
            public String gender;
            public int age;
            public int hp1;
            public int hp2;
            public int hp3;

            private Builder(String mbrId, String mbrPw) {
                this.mbrId = mbrId;
                this.mbrPw = mbrPw;
            }

            public Builder with(Consumer<Builder> consumer){
                consumer.accept(this);
                return this;
            }

            public Member build(){
                return new Member(this);
            }
        }

        public long getMbrSn() {
            return mbrSn;
        }
        public String getMbrId() {
            return mbrId;
        }
        public String getMbrPw() {
            return mbrPw;
        }
        public String getMbrNm() {
            return mbrNm;
        }
        public String getEmail() {
            return email;
        }
        public String getGender() {
            return gender;
        }
        public int getAge() {
            return age;
        }
        public int getHp1() {
            return hp1;
        }
        public int getHp2() {
            return hp2;
        }
        public int getHp3() {
            return hp3;
        }

        @Override
        public String toString() {
            return "Member{" +
                    "mbrSn=" + mbrSn +
                    ", mbrId='" + mbrId + '\'' +
                    ", mbrPw='" + mbrPw + '\'' +
                    ", mbrNm='" + mbrNm + '\'' +
                    ", email='" + email + '\'' +
                    ", gender='" + gender + '\'' +
                    ", age=" + age +
                    ", hp1=" + hp1 +
                    ", hp2=" + hp2 +
                    ", hp3=" + hp3 +
                    '}';
        }
    }

    public static void main(String[] args) {
        Member builderMember = Member.builder("quedevel", "1234").with(builder -> {}).build();
        System.out.println("builderMember.toString() = " + builderMember.toString());
    }
}
```

## 아이템3. private생성자나 열거 타입으로 싱글턴임을 보증하라.

### Item3
```java
public class Item3 {

    static class Singleton {

        // LazyHolder Singleton pattern
        private Singleton(){};

        private static class LazyHolder{
            public static final Singleton instance = new Singleton();
        }

        // getInstance()를 호출했을때 LazyHolder 클래스가 로딩되면서 생성
        // 장점 : 객체가 필요한 시점에서 초기화가 진행된다.
        public static Singleton getInstance(){
            return LazyHolder.instance;
        }
    }

    public static void main(String[] args) {
        Singleton singleton1 = Singleton.getInstance();
        Singleton singleton2 = Singleton.getInstance();

        System.out.println(singleton1);
        System.out.println(singleton2);
        System.out.println(singleton1 == singleton2);

    }
}
```

## 아이템4. 인스턴스화를 막으려거든 private 생성자를 사용하라.

### Item4
```java
public class Item4 {
    static class StringUtils {

        private StringUtils(){}

        public static boolean isEmpty(final CharSequence cs) {
            return cs == null || cs.length() == 0;
        }
    }

    public static void main(String[] args) {
        System.out.println(StringUtils.isEmpty(null));      // true
        System.out.println(StringUtils.isEmpty(""));        // true
        System.out.println(StringUtils.isEmpty(" "));       // false
        System.out.println(StringUtils.isEmpty("bob"));     // false
        System.out.println(StringUtils.isEmpty("  bob  ")); // false
    }
}
```

## 아이템5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라.

### Item5
```java
public class Item5 {

    static abstract class Lexicon {}
    static class EnglishLexicon extends Lexicon {}
    static class KoreanLexicon extends Lexicon {}
    static class SpellChecker {
        private Lexicon dictionary;
        public SpellChecker(Supplier<? extends Lexicon> supplier){
            this.dictionary = supplier.get();
        }
        public boolean isValid(String word){return false;}
    }

}
```

## 아이템6. 불필요한 객체 생성을 피하라.

### Item6
```java
public class Item6 {
    public static void main(String[] args) {
        // 끔찍하게 느린 박싱타입 계산
        Long sum = 0L;
        for (long i = 0; i < Integer.MAX_VALUE; i++) {
            sum += i;
        }
        System.out.println(sum);
    }
}
```

## 아이템9. try-finally보다는 try-with-resources를 사용하라.

### Item9
```java
public class Item9 {

    public static void main(String[] args){
        try(BufferedReader br = new BufferedReader(new FileReader("try-with-resources"))){

        } catch (IOException e) {
            e.getStackTrace();
        }
    }
}
```
