# π₯ [ Chapter7 ] λλ€μ μ€νΈλ¦Ό

## π―  μμ΄ν 42. μ΅λͺ ν΄λμ€λ³΄λ€λ λλ€λ₯Ό μ¬μ©νλΌ.
μμ μλ μλ°μμ ν¨μ νμμ ννν  λ μΆμ λ©μλλ₯Ό νλλ§ λ΄μ μΈν°νμ΄μ€λ₯Ό μ¬μ©νλ€.<br>
μ΄λ° μΈν°νμ΄μ€μ μΈμ€ν΄μ€λ₯Ό ν¨μκ°μ²΄λΌκ³  νμ¬, νΉμ  ν¨μλ λμμ λνλ΄λ λ° μΌλ€.<br>

<br>

* μ΅λͺ ν΄λμ€μ μΈμ€ν΄μ€λ₯Ό ν¨μ κ°μ²΄λ‘ μ¬μ© - λ‘μ κΈ°λ²μ΄λ€!
```java
public class Item43 {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("test3");
        list.add("te2st3");
        list.add("test4");
        list.add("5test3");
        
        Collections.sort(list, new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                return Integer.compare(o1.length(), o2.length());
            }
        });

        list.forEach(System.out::println);
    }
}
```
μ λ΅ ν¨ν΄μ²λΌ, ν¨μ κ°μ²΄λ₯Ό μ¬μ©νλ κ³Όκ±° κ°μ²΄ μ§ν₯ λμμΈ ν¨ν΄μλ μ΅λͺ ν΄λμ€λ©΄ μΆ©λΆνλ€.

<br>

μλ°8μ μμ μΆμ λ©μλ νλμ§λ¦¬ μΈν°νμ΄μ€λ νΉλ³ν μλ―Έλ₯Ό μΈμ λ°μ νΉλ³ν λμ°λ₯Ό λ°κ² λμλ€.<br>
μ§κΈμ ν¨μν μΈν°νμ΄μ€λΌ λΆλ₯΄λ μ΄ μΈν°νμ΄μ€λ€μ μΈμ€ν΄μ€λ₯Ό λλ€μμ μ¬μ©ν΄ λ§λ€ μ μκ² λ κ²μ΄λ€.<br>

<br>

* λλ€μμ ν¨μ κ°μ²΄λ‘ μ¬μ© - μ΅λͺ ν΄λμ€ λμ²΄
```java
public class Item43 {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("test3");
        list.add("te2st3");
        list.add("test4");
        list.add("5test3");

        // 1
        Collections.sort(list, (s1, s2) -> Integer.compare(s1.length(), s2.length()));
        
        // 2
        Collections.sort(list, comparingInt(String::length));
        
        // 3 
        list.sort(comparingInt(String::length));

        list.forEach(System.out::println);
    }
}
```
**_νμμ λͺμν΄μΌ μ½λκ° λ λͺνν  λλ§ μ μΈνκ³ λ, λλ€μ λͺ¨λ  λ§€κ°λ³μ νμμ μλ΅νμ._** <br>

<br>

> λλ€λ μ΄λ¦μ΄ μκ³  λ¬Έμνλ λͺ»νλ€. λ°λΌμ μ½λ μμ²΄λ‘ λμμ΄ λͺνν μ€λͺλμ§ μκ±°λ μ½λ μ€ μκ° λ§μμ§λ©΄ λλ€λ₯Ό μ°μ§ λ§μμΌ νλ€. <br>

<br>

## π―  μμ΄ν 43. λλ€λ³΄λ€λ λ©μλ μ°Έμ‘°λ₯Ό μ¬μ©νλΌ.
λλ€κ° μ΅λͺ ν΄λμ€λ³΄λ€ λμ μ  μ€μμ κ°μ₯ ν° νΉμ§μ κ°κ²°ν¨μ΄λ€. κ·Έλ°λ° μλ°μλ ν¨μ κ°μ²΄λ₯Ό μ¬μ§μ΄ λλ€λ³΄λ€λ <br>
λ κ°κ²°νκ² λ§λλ λ°©λ²μ΄ μμΌλ, λ°λ‘ λ©μλ μ°Έμ‘°λ€. νμ§λ§, λ©μλ μ°Έμ‘° μͺ½μ΄ μ§§κ³  λͺννλ€λ©΄ λ©μλ μ°Έμ‘°λ₯Ό μ°κ³ , <br>
κ·Έλ μ§ μμ λλ§ λλ€λ₯Ό μ¬μ©νλΌ.<br>

<br>


* λ©μλ μ°Έμ‘°λ₯Ό μ¬μ©νμλ λ μ§§κ³  λͺνν μμ€
```java
List<Long> sortedOrders = orders.stream()
        .sorted(comparing(Order::getCreatedAt))
        .map(Order::getId)
        .collect(Collectors.toList());
```
μ’ λ μ§§κ³  λͺνν μμ€κ° λμλ€. νμ§λ§, ν΄λμ€λͺμ΄ λλ¬΄ κΈΈκ±°λ λ λͺννλ€κ³  νλ¨λμ§ μλλ€λ©΄ λλ€λ₯Ό μ¬μ©νμ.

## π―  μμ΄ν 44. νμ€ ν¨μν μΈν°νμ΄μ€λ₯Ό μ¬μ©νλΌ.
**_νμν μ©λμ λ§λ κ² μλ€λ©΄, μ§μ  κ΅¬ννμ§ λ§κ³  νμ€ ν¨μν μΈν°νμ΄μ€λ₯Ό νμ©νλΌ._**<br>
```java
public class Item44 {
    public static void main(String[] args) {

        /**
         * 1 Supplier
         */
        Supplier<String> supplier = () -> "hello world";
        System.out.println("supplier = " + supplier.get());

        Supplier<Double> doubleSupplier = () -> Math.random();
        System.out.println("doubleSupplier = " + doubleSupplier.get());

        printRandomDoubles(doubleSupplier, 5);

        System.out.println();

        /**
         * 2 Consumer
         */
        Consumer<String> stringConsumer = str -> System.out.println(str);
        stringConsumer.accept("Hell World");

        Consumer<Integer> integerConsumer = x -> System.out.println("Processing Integer " + x);
        List<Integer> integerList = Arrays.asList(4,2,3);

        process(integerList, integerConsumer);
        System.out.println();

        /**
         * 3 BiConsumer
         */
        BiConsumer<Integer, Double> doubleBiConsumer = (index, input) -> System.out.println("Processing " + input+" at index "+index);
        List<Double> doubleList = Arrays.asList(4.1,2.2,3.3);

        process(doubleList, doubleBiConsumer);
        System.out.println();

        /**
         * 4 Predicate
         */
        Predicate<Integer> integerPredicate = x -> x>0;
        System.out.println(integerPredicate.test(2));

        List<Integer> inputs = Arrays.asList(10,-5,-2,0,3);
        System.out.println("filter(inputs, integerPredicate) = " + filter(inputs, integerPredicate));
        System.out.println("filter(inputs, integerPredicate.negate()) = " + filter(inputs, integerPredicate.negate()));
        System.out.println("filter(inputs, integerPredicate.or(x-> x==0)) = " + filter(inputs, integerPredicate.or(x-> x==0)));
        System.out.println("filter(inputs, integerPredicate.and(x-> x%2 == 0)) = " + filter(inputs, integerPredicate.and(x-> x%2 == 0)));
        System.out.println();

        /**
         * 5 Comparator
         */
        List<User> users = new ArrayList<>();
        users.add(new User(3, "Alice"));
        users.add(new User(1, "Charlie"));
        users.add(new User(5, "Bob"));
        System.out.println("users = " + users);

        Comparator<User> idComparator = Comparator.comparingInt(User::getId);
        Collections.sort(users, idComparator);
        System.out.println("users = " + users);

        Collections.sort(users, Comparator.comparing(User::getName));
        System.out.println("users = " + users);
    }

    public static void printRandomDoubles(Supplier<Double> randomSupplier, int count){
        for (int i = 0; i < count; i++){
            System.out.println(randomSupplier.get());
        }
    }

    public static <T> void process(List<T> inputs, Consumer<T> processor){
        for (T input : inputs){
            processor.accept(input);
        }
    }

    public static <T> void process(List<T> inputs, BiConsumer<Integer, T> processor){
        for (int i = 0; i < inputs.size(); i++) {
            processor.accept(i, inputs.get(i));
        }
    }

    public static <T> List<T> filter(List<T> inputs, Predicate<T> condition){
        List<T> output = new ArrayList<>();
        for (T t : inputs) {
            if(condition.test(t)){
                output.add(t);
            }
        }
        return output;
    }

    static class User {
        private int id;
        private String name;
        public User(int id, String name) {
            this.id = id;
            this.name = name;
        }
        public int getId() {return id;}
        public void setId(int id) {this.id = id;}
        public String getName() {return name;}
        public void setName(String name) {this.name = name;}
        @Override
        public String toString() {return "User{" + "id=" + id + ", name='" + name + '\'' + '}';}
    }
}
```
μλ νμ€ ν¨μν μΈν°νμ΄μ€λ₯Ό νμ©ν κ°λ¨ν μμ μ΄λ€. <br>

<br>

νμ§λ§, νμ€ ν¨μν μΈν°νμ΄μ€κ° μ§μνμ§ μλ κΈ°λ₯μ λ§λ€κ³ μ νλ€λ©΄ λ°λμ `@FuntionalInterface`λ₯Ό μ¬μ©νμ. <br>
μ΄ μ λνμ΄μμ μ¬μ©νλ μ΄μ λ `@Override`λ₯Ό μ¬μ©νλ μ΄μ μ λΉμ·νλ€.
1. ν΄λΉ ν΄λμ€μ μ½λλ μ€λͺ λ¬Έμλ₯Ό μ½μ μ΄μκ² κ·Έ μΈν°νμ΄μ€κ° λλ€μ©μΌλ‘ μ€κ³λ κ²μμ μλ €μ€λ€.
2. ν΄λΉ μΈν°νμ΄μ€κ° μΆμ λ©μλλ₯Ό μ€μ§ νλλ§ κ°μ§κ³  μμ΄μΌ μ»΄νμΌλκ² ν΄μ€λ€.
3. κ·Έ κ²°κ³Ό μ μ§λ³΄μ κ³Όμ μμ λκ΅°κ° μ€μλ‘ λ©μλλ₯Ό μΆκ°νμ§ λͺ»νκ² λ§μμ€λ€.

## π―  μμ΄ν 45. μ€νΈλ¦Όμ μ£Όμν΄μ μ¬μ©νλΌ.
μ€νΈλ¦Ό APIλ λ€μ¬λ€λ₯νμ¬ μ¬μ€μ μ΄λ ν κ³μ°μ΄λΌλ ν΄λΌ μ μλ€. νμ§λ§ ν  μ μλ€λ λ»μ΄μ§, ν΄μΌ νλ€λ λ»μ μλλ€!!<br>
μ€νΈλ¦Όμ μ λλ‘ μ¬μ©νλ©΄ νλ‘κ·Έλ¨μ΄ μ§§κ³  κΉκΈν΄μ§μ§λ§, μλͺ» μ¬μ©νλ©΄ μ½κΈ° μ΄λ ΅κ³  μ μ§λ³΄μλ νλ€μ΄μ§λ€.<br>

<br>

* μ¬μ  νλλ₯Ό νμ΄ μμ μκ° λ§μ μλκ·Έλ¨ κ·Έλ£Ήλ€μ μΆλ ₯νλ€.
```java
public class IterativeAnagrams {
    public static void main(String[] args) throws IOException {
        String[] sArr = new String[]{"abc","euq","test","zzfq","que","acb"};
        int minGroupSize = Integer.parseInt("1");

        Map<String, Set<String>> groups = new HashMap<>();

        for (String s : sArr) {
            String word = s;
            groups.computeIfAbsent(alphabetize(word),
                    (unused) -> new TreeSet<>()).add(word);
        }

        for (Set<String> group : groups.values())
            if (group.size() >= minGroupSize)
                System.out.println(group.size() + ": " + group);
    }

    private static String alphabetize(String s) {
        char[] a = s.toCharArray();
        Arrays.sort(a);
        return new String(a);
    }
}
```

<br>

* μ€νΈλ¦Όμ κ³Όνκ² μ¬μ©νλ€.
```java
public class StreamAnagrams {
    public static void main(String[] args) throws IOException {
        int minGroupSize = Integer.parseInt("1");
        Stream<String> words = Stream.of("abc","euq","test","zzfq","que","acb");

        words.collect(
                        groupingBy(word -> word.chars().sorted()
                                .collect(StringBuilder::new,
                                        (sb, c) -> sb.append((char) c),
                                        StringBuilder::append).toString()))
                .values().stream()
                .filter(group -> group.size() >= minGroupSize)
                .map(group -> group.size() + ": " + group)
                .forEach(System.out::println);
    }
}
```
**_μ€νΈλ¦Όμ κ³Όμ©νλ©΄ νλ‘κ·Έλ¨μ΄ μ½κ±°λ μ μ§λ³΄μνκΈ° μ΄λ €μμ§λ€._**<br>

<br>

* μ€νΈλ¦Όμ μ μ ν νμ©νλ©΄ κΉλνκ³  λͺλ£ν΄μ§λ€.
```java
public class HybridAnagrams {
    public static void main(String[] args) throws IOException {
        Stream<String> words = Stream.of("abc","euq","test","zzfq","que","acb");
        int minGroupSize = Integer.parseInt("1");

        words.collect(groupingBy(word -> alphabetize(word)))
                .values().stream()
                .filter(group -> group.size() >= minGroupSize)
                .forEach(g -> System.out.println(g.size() + ": " + g));
    }

    private static String alphabetize(String s) {
        char[] a = s.toCharArray();
        Arrays.sort(a);
        return new String(a);
    }
}
```

<br>

κΈ°μ‘΄ μ½λλ μ€νΈλ¦Όμ μ¬μ©νλλ‘ λ¦¬ν©ν°λ§νλ, μ μ½λκ° λ λμ λ³΄μΌ λλ§ λ°μνμ.<br>
μ€νΈλ¦Όμ΄ μμ£Ό μμ±λ§μΆ€μΈ μ‘°κ±΄
1. μμλ€μ μνμ€λ₯Ό μΌκ΄λκ² λ³ννλ€.
2. μμλ€μ μνμ€λ₯Ό νν°λ§νλ€.
3. μμλ€μ μνμ€λ₯Ό νλμ μ°μ°μ μ¬μ©ν΄ κ²°ν©νλ€.
4. μμλ€μ μνμ€λ₯Ό μ»¬λ μμ λͺ¨μλ€.
5. μμλ€μ μνμ€μμ νΉμ  μ‘°κ±΄μ λ§μ‘±νλ μμλ₯Ό μ°Ύλλ€.

<br>

## π―  μμ΄ν 46. μ€νΈλ¦Όμμλ λΆμμ© μλ ν¨μλ₯Ό μ¬μ©νλΌ.
μ€νΈλ¦Ό νμ΄νλΌμΈ νλ‘κ·Έλλ°μ ν΅μ¬μ λΆμμ© μλ ν¨μ κ°μ²΄μ μλ€. μ€νΈλ¦ΌλΏ μλλΌ μ€νΈλ¦Ό κ΄λ ¨ κ°μ²΄μ κ±΄λ€μ§λ <br>
λͺ¨λ  ν¨μ κ°μ²΄κ° λΆμμ©μ΄ μμ΄μΌ νλ€. μ’λ¨ μ°μ° μ€ `forEach`λ μ€νΈλ¦Όμ΄ μνν κ³μ° κ²°κ³Όλ₯Ό λ³΄κ³ ν  λλ§ μ΄μ©ν΄μΌ νλ€. <br>
κ³μ  μμ²΄μλ μ΄μ©νμ§ λ§μ. μ€νΈλ¦Όμ μ¬λ°λ‘ μ¬μ©νλ €λ©΄ μμ§κΈ°λ₯Ό μ μμλ¬μΌ νλ€. κ°μ₯ μ€μν μμ§κΈ° ν©ν°λ¦¬λ <br>
`toList`, `toSet`, `toMap`, `groupingBy`, `joining`μ΄λ€.<br>

<br>

* Collectors
```java
/**
 * Collectors
 */
List<Integer> integerList = Stream.of(3, 5, -3, 3, 4, 5).collect(java.util.stream.Collectors.toList());
System.out.println("integerList = " + integerList);

Set<Integer> integerSet = Stream.of(3, 5, -3, 3, 4, 5).collect(java.util.stream.Collectors.toSet());
System.out.println("integerSet = " + integerSet);

List<Integer> integerList1 = Stream.of(3, 5, -3, 3, 4, 5).collect(java.util.stream.Collectors.mapping(x -> Math.abs(x), java.util.stream.Collectors.toList()));
System.out.println("integerList1 = " + integerList1);

Set<Integer> integerSet1 = Stream.of(3, 5, -3, 3, 4, 5).collect(java.util.stream.Collectors.mapping(x -> Math.abs(x), java.util.stream.Collectors.toSet()));
System.out.println("integerSet1 = " + integerSet1);

int sum = Stream.of(3, 5, -3, 3, 4, 5).collect(java.util.stream.Collectors.reducing(0, (x,y)->x+y));
System.out.println("sum = " + sum);
```

<br>

* groupingBy
```java
/**
 * Grouping By
 */
List<Integer> integerList = Arrays.asList(13, 2, 101, 203, 304, 402, 305, 349, 2312, 203, 70);
Map<Integer, List<Integer>> integerListMap = integerList.stream().collect(Collectors.groupingBy(n -> n % 10));
System.out.println("integerListMap = " + integerListMap);

Map<Integer, List<String>> integerListMap1 = integerList.stream()
        .collect(Collectors.groupingBy(n -> n % 10, Collectors.mapping(n -> "unit digit is " + n, Collectors.toList())));
System.out.println("integerListMap1 = " + integerListMap1);
```

<br>

* PartitioningBy
```java
/**
 * Partitioning By
 */
List<Integer> integerList = Arrays.asList(13, 2, 101, 203, 304, 402, 305, 349, 2312, 203, 70);
Map<Boolean, List<Integer>> booleanListMap = integerList.stream()
        .collect(Collectors.partitioningBy(n -> n % 2 == 0));
System.out.println("booleanListMap = " + booleanListMap);
```


## π―  μμ΄ν 47. λ°ν νμμΌλ‘λ μ€νΈλ¦Όλ³΄λ€ μ»¬λ μμ΄ λ«λ€.
μμ μνμ€λ₯Ό λ°νΈλνλ λ©μλλ₯Ό μμ±ν  λλ, μ΄λ₯Ό μ€νΈλ¦ΌμΌλ‘ μ²λ¦¬νκΈ°λ₯Ό μνλ μ¬μ©μμ λ°λ³΅μΌλ‘ μ²λ¦¬νκΈΈ μνλ μ¬μ©μκ° <br>
λͺ¨λ μμ μ μμμ λ μ¬λ¦¬κ³ , μμͺ½μ λ€ λ§μ‘±μν€λ € λΈλ ₯νμ. μ»¬λ μμ λ°νν  μ μλ€λ©΄ κ·Έλ κ² νλΌ. νμ§λ§ λ¨μ§ μ»¬λ μμ λ°ννλ€λ<br>
μ΄μ λ‘ λ©μΉ ν° μνμ€λ₯Ό λ©λͺ¨λ¦¬μ μ¬λ €μλ μλλ€.

<br>

## π―  μμ΄ν 48. μ€νΈλ¦Ό λ³λ ¬νλ μ£Όμν΄μ μ μ©νλΌ.
κ³μ°λ μ¬λ°λ‘ μννκ³  μ±λ₯λ λΉ¨λΌμ§ κ±°λΌλ νμ  μμ΄λ μ€νΈλ¦Ό νμ΄νλΌμΈ λ³λ ¬νλ μλμ‘°μ°¨ νμ§ λ§λΌ. <br>
* μ£Όμμ 
1. λ°μ΄ν° μμ€κ° Stream.iterateκ±°λ μ€κ° μ¬μ°μΌλ‘ limitλ₯Ό μ°λ©΄ νμ΄νλΌμΈ λ³λ ¬νλ‘λ μ±λ₯ κ°μ μ κΈ°λν  μ μλ€.
2. μ€νΈλ¦Όμ μμ€κ° ArrayList, HashMap, HashSet, ConcurrentHashMapμ μΈμ€ν΄μ€κ±°λ λ°°μ΄, int λ²μ, long λ²μμΌ λ λ³λ ¬νμ ν¨κ³Όκ° κ°μ₯ μ’λ€.
3. μ€νΈλ¦Όμ μλͺ» λ³λ ¬ννλ©΄ μ±λ₯μ΄ λλΉ μ§ λΏλ§ μλλΌ κ²°κ³Ό μμ²΄κ° μλͺ»λκ±°λ μμ λͺ»ν λμμ΄ λ°μν  μ μλ€.

<br>

νμ§λ§, μ‘°κ±΄μ΄ μ κ°μΆ°μ§λ©΄ parallel λ©μλ νΈμΆ νλλ‘ κ±°μ νλ‘μΈμ μ½μ΄ μμ λΉλ‘νλ μ±λ₯ ν₯μμ λ§λ½ν  μ μλ€.


<br>

> Joshua Bloch, γEffective Java 3/Eγ, κ°μλ§΅μ μ?κΉ, νλ‘κ·Έλλ°μΈμ¬μ΄νΈ(2018), p253-296.