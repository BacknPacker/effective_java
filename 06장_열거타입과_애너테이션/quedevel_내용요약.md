# π₯ [ Chapter6 ] μ΄κ±° νμκ³Ό μ λνμ΄μ


## π―  μμ΄ν 34. int μμ λμ  μ΄κ±° νμμ μ¬μ©νλΌ.
* μ μ μ΄κ±° ν¨ν΄ - μλΉν μ·¨μ½νλ€!
```java
public class IntegerConstants {
    public static final int APPLE_FUJI = 0;
    public static final int APPLE_PIPPIN = 1;
    public static final int APPLE_GRANNY_SMITH = 2;

    public static final int ORANGE_NAVEL = 0;
    public static final int ORANGE_TEMPLE = 1;
    public static final int ORANGE_BLOOD = 2;
}
```
μ κ°μ μ μ μ΄κ±° ν¨ν΄μ νμ μμ μ λ³΄μ₯ν  λ°©λ²μ΄ μμΌλ©° ννλ ₯λ μ’μ§ μλ€.<br>
μ€λ μ§λ₯Ό κ±΄λ€μΌ ν  λ©μλμ μ¬κ³Όλ₯Ό λ³΄λ΄κ³  `λλ± μ°μ°μ(==)`λ‘ λΉκ΅νλλΌλ μ»΄νμΌλ¬λ μλ¬΄λ° κ²½κ³  λ©μμ§λ₯Ό μΆλ ₯νμ§ μλλ€.<br>


<br>

* κ°μ₯ λ¨μν μ΄κ±° νμ
```java
public enum Apple { FUJI, PIPPIN, GRANNY_SMITH }
public enum Orange { NAVEL, TEMPLE, BLOOD }
```
μλ°μ μ΄κ±° νμμ μμ ν ννμ ν΄λμ€λΌμ λ€λ₯Έ μΈμ΄μ μ΄κ±° νμλ³΄λ€ ν¨μ¬ κ°λ ₯νλ€.<br>
μ΄κ±° νμμ λ°μμ μ κ·Όν  μ μλ μμ±μλ₯Ό μ κ³΅νμ§ μμΌλ―λ‘ μ¬μ€μ `final`μ΄λ€.<br>
λ°λΌμ ν΄λΌμ΄μΈνΈκ° μΈμ€ν΄μ€λ₯Ό μ§μ  μμ±νκ±°λ νμ₯ ν  μ μμΌλ μ΄κ±° νμ μ μΈμΌλ‘ λ§λ€μ΄μ§ μΈμ€ν΄μ€λ€μ λ± νλμ©λ§ μ‘΄μ¬ν¨μ΄ λ³΄μ₯λλ€.<br>
<span style="color:red; font-weight: bold;">if</span>. `Apple` μ΄κ±° νμμ λ§€κ°λ³μλ‘ λ°λ λ©μλλ₯Ό μ μΈνλ€λ©΄, κ±΄λ€λ°μ μ°Έμ‘°λ `Apple`μ μΈ κ°μ§ κ° μ€ νλμμ΄ νμ€νλ€.<br>
λ€λ₯Έ νμμ κ°μ λκΈ°λ € νλ©΄ μ»΄νμΌ μ€λ₯κ° λλ€.<br>

<br>

* λ°μ΄ν°μ λ©μλλ₯Ό κ°λ μ΄κ±° νμ
```java
public enum Planet {
    MERCURY(3.302e+23, 2.439e6),
    VENUS  (4.869e+24, 6.052e6),
    EARTH  (5.975e+24, 6.378e6),
    MARS   (6.419e+23, 3.393e6),
    JUPITER(1.899e+27, 7.149e7),
    SATURN (5.685e+26, 6.027e7),
    URANUS (8.683e+25, 2.556e7),
    NEPTUNE(1.024e+26, 2.477e7);

    private final double mass;           // μ§λ(λ¨μ: ν¬λ‘κ·Έλ¨)
    private final double radius;         // λ°μ§λ¦(λ¨μ: λ―Έν°)
    private final double surfaceGravity; // νλ©΄μ€λ ₯(λ¨μ: m / s^2)

    // μ€λ ₯μμ(λ¨μ: m^3 / kg s^2)
    private static final double G = 6.67300E-11;

    // μμ±μ
    Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
        surfaceGravity = G * mass / (radius * radius);
    }

    public double mass()           { return mass; }
    public double radius()         { return radius; }
    public double surfaceGravity() { return surfaceGravity; }

    public double surfaceWeight(double mass) {
        return mass * surfaceGravity;  // F = ma
    }
}
```
_**μ΄κ±° νμ μμ κ°κ°μ νΉμ  λ°μ΄ν°μ μ°κ²°μ§μΌλ €λ©΄ μμ±μμμ λ°μ΄ν°λ₯Ό λ°μ μΈμ€ν΄μ€ νλμ μ μ₯νλ©΄λλ€.**_ <br>

<br>

μμ Planet μμμ λ³΄μ¬μ€ νΉμ±λ§μΌλ‘ μ°κ±° νμμ μ¬μ©νλ μν© λλ€μλ₯Ό νλ₯­ν μ€λͺν  μ μλ€.<br>
νμ§λ§ μμκ° λ λ€μν κΈ°λ₯μ μ κ³΅ν΄μ€¬μΌλ©΄ ν  λλ μλ€.<br>

<br>

* κ°μ λ°λΌ λΆκΈ°νλ μ΄κ±° νμ - μ΄λλ‘ λ§μ‘±νλκ°?
```java
public enum Operation {
    PLUS, MINUS, TIMES, DIVIDE;
    
    public double apply(double x, double y){
        switch (this){
            case PLUS: return x+y;
            case MINUS: return x-y;
            case TIMES: return x*y;
            case DIVIDE: return x/y;
        }
        throw new AssertionError("μ μ μλ μ°μ°: " + this);
    }
}
```
μ μμ€λ λμμ νμ§λ§ λ€λ₯Έ μ°μ°μ μΆκ°ν λλ caseλ¬Έμ μΆκ°λ₯Ό ν΄μΌνλ€.<br>
λ§μ½ κΉλ¨Ήκ³  caseλ¬Έμ μΆκ°νμ§ μλλ€ νλλΌλ μ»΄νμΌ μλ¬κ° λ°μνμ§ μκ³  λ°νμμ€ "μ μ μλ μ°μ°"μ΄λΌλ μ€λ₯λ₯Ό λ°μνλ€.<br>
λ°λΌμ μμλ³ ν΄λμ€ λͺΈμ²΄, μ¦ κ° μμμμ μμ μ λ§κ² μ¬μ μνλ λ°©λ²μ΄ μλ€.<br>

<br>

* μμλ³ λ©μλ κ΅¬νμ νμ©ν μ΄κ±° νμ
```java
public enum Operation2 {
    PLUS { public double apply(double x, double y){ return x+y; } },
    MINUS { public double apply(double x, double y){ return x-y; } },
    TIMES { public double apply(double x, double y){ return x*y; } },
    DIVIDE { public double apply(double x, double y){ return x/y; } };

    public abstract double apply(double x, double y); // μΆμ λ©μλλ₯Ό μ μΈ
}
```
μ μ²λΌ μΆμλ©μλ `apply`λ₯Ό μ μΈνλ©΄ λ€λ₯Έ μ°μ°μ μΆκ°ν  λλ λ©μλ κ΅¬νμ²΄λ₯Ό κ°μ΄ μΆκ°νμ§ μλλ€λ©΄ <br>
μ»΄νμΌ μ€λ₯κ° λ°μνκΈ° λλ¬Έμ μμ νλ€.

<br>

## π―  μμ΄ν 35. ordinal λ©μλ λμ  μΈμ€ν΄μ€ νλλ₯Ό μ¬μ©νλΌ.
μ΄κ±° νμμ ν΄λΉ μμκ° κ·Έ μ΄κ±° νμμμ λͺ λ²μ§Έ μμΉμΈμ§λ₯Ό λ°ννλ `ordinal`μ΄λΌλ λ©μλλ₯Ό μ κ³΅νλ€.
```java
public enum RoleType {
    USER, ADMIN;
}
```
```java
@Entity
public class Member {
    @Id
    private Long id;

    @Column(name = "name")
    private String username;

    private Integer age;

    @Enumerated(EnumType.ORDINAL)
    private RoleType roleType;
}    
```
μ μ²λΌ `javax.persistence`μμ μ κ³΅νλ νμμ enumμΌλ‘ μ§μ νλ `@Enumerated`μ μ κ³΅ν΄μ€λ€.<br>
`@Enumerated` μ λνμ΄μμλ λ κ°μ§ μ΅μμ΄ μλλ° `EnumType.ORDINAL`κ³Ό `EnumType.STRING`μ΄ μλλ° <br>
`EnumType.ORDINAL`μ μ΅μμΌλ‘ λ£μΌλ©΄ λ΄λΆμ μΌλ‘ `ordinal`ν¨μλ₯Ό μ¬μ©νκΈ° λλ¬Έμ enumκ°μ μμλ‘ μ μ₯νκ² λλλ°<br>
μ¬κΈ°μ λ§μ½ `RoleType`μ μΆκ°κ° λλ€λ©΄ `GUEST`λΌλ κ°μ΄ μμμ μΆκ°κ° λλ€λ©΄ `USER`λ‘ μ μ₯λ κ°μ `GUEST`λ‘ λκΈ° λλ¬Έμ μννλ€.<br>
```java
public enum RoleType {
    GUEST, USER, ADMIN;
}
```
λ°λΌμ, νΉλ³ν μ©λκ° μλλΌλ©΄ `ordinal` λ©μλλ μ λ μ¬μ©νμ§ λ§μ

<br>

## π―  μμ΄ν 36. λΉνΈ νλ λμ  EnumSetμ μ¬μ©νλΌ.
* λΉνΈ νλ μ΄κ±° μμ - κ΅¬λ₯λ€λ¦¬ κΈ°λ²!
```java
public class Text {
    public static final int STYLE_BOLD          = 1 << 0; // 1
    public static final int STYLE_ITALIC        = 1 << 1; // 2
    public static final int STYLE_UNDERLINE     = 1 << 2; // 4
    public static final int STYLE_STRIKETHROUGH = 1 << 3; // 5
    
    public void applyStyles(int styles){ ... }
}
```
λ€μκ³Ό κ°μ μμΌλ‘ λΉνΈλ³ ORλ₯Ό μ¬μ©ν΄ μ¬λ¬ μμλ₯Ό νλμ μ§ν©μΌλ‘ λͺ¨μ μ μμΌλ©°, μ΄λ κ² λ§λ€μ΄μ§ μ§ν©μ λΉνΈ νλλΌ νλ€. <br>
```java
text.applyStyles(STYLE_BOLD | STYLE_ITALIC);
```
νμ§λ§ λΉνΈ νλλ μ μ μ΄κ±° μμμ λ¨μ μ κ·Έλλ‘ μ§λλ©°, μΆκ°λ‘ λ€μκ³Ό κ°μ λ¬Έμ κΉμ§ μλ€. <br>
λΉνΈ νλ κ°μ΄ κ·Έλλ‘ μΆλ ₯λλ©΄ λ¨μν μ μ μ΄κ±° μμλ₯Ό μΆλ ₯ν  λλ³΄λ€ ν΄μνκΈ°κ° μ΄λ ΅λ€.<br>

<br>

* EnumSet - λΉνΈ νλλ₯Ό λμ²΄νλ νλμ  κΈ°λ²
```java
public class Text {
    public enum Style {BOLD, ITALIC, UNDERLINE, STRIKETHROUGH}

    // μ΄λ€ Setμ λκ²¨λ λλ, EnumSetμ΄ κ°μ₯ μ’λ€.
    public void applyStyles(Set<Style> styles) {
        System.out.printf("Applying styles %s to text%n", Objects.requireNonNull(styles));
    }

    // μ¬μ© μ
    public static void main(String[] args) {
        Text text = new Text();
        text.applyStyles(EnumSet.of(Style.BOLD, Style.ITALIC));
    }
}
```
**_EnumSet ν΄λμ€κ° λΉνΈ νλ μμ€μ λͺλ£ν¨κ³Ό μ±λ₯μ μ κ³΅νκ³  μ΄κ±° νμμ μ₯μ κΉμ§ μ μ¬νλ€._**

## π―  μμ΄ν 37. ordinal μΈλ±μ± λμ  EnumMapμ μ¬μ©νλΌ.
```java
public class Plant {
    enum LifeCycle { ANNUAL, PERENNIAL, BIENNIAL }

    final String name;
    final LifeCycle lifeCycle;

    Plant(String name, LifeCycle lifeCycle) {
        this.name = name;
        this.lifeCycle = lifeCycle;
    }

    @Override public String toString() {
        return name;
    }
}
```
* ordinal()μ λ°°μ΄ μΈλ±μ€λ‘ μ¬μ© - λ°λΌ νμ§ λ§ κ²!
```java
public static void main(String[] args) {
    Plant[] garden = {
            new Plant("λ°μ§",    LifeCycle.ANNUAL),
            new Plant("μΊλ¬μ¨μ΄", LifeCycle.BIENNIAL),
            new Plant("λ",      LifeCycle.ANNUAL),
            new Plant("λΌλ²€λ",   LifeCycle.PERENNIAL),
            new Plant("νμ¬λ¦¬",   LifeCycle.BIENNIAL),
            new Plant("λ‘μ¦λ§λ¦¬", LifeCycle.PERENNIAL)
    };

    // μ½λ 37-1 ordinal()μ λ°°μ΄ μΈλ±μ€λ‘ μ¬μ© - λ°λΌ νμ§ λ§ κ²! (226μͺ½)
    Set<Plant>[] plantsByLifeCycleArr = (Set<Plant>[]) new Set[Plant.LifeCycle.values().length];
    for (int i = 0; i < plantsByLifeCycleArr.length; i++){
        plantsByLifeCycleArr[i] = new HashSet<>();
    }
    for (Plant p : garden) {
        plantsByLifeCycleArr[p.lifeCycle.ordinal()].add(p);
    }
    // κ²°κ³Ό μΆλ ₯
    for (int i = 0; i < plantsByLifeCycleArr.length; i++) {
        System.out.printf("%s: %s%n", Plant.LifeCycle.values()[i], plantsByLifeCycleArr[i]);
    }
}
```
* μΆλ ₯ κ²°κ³Ό
  <br>
  <img width="40%" src="https://user-images.githubusercontent.com/55771326/168415411-54f410ea-2a57-48f0-bdc7-7c618548bf1c.PNG">
  <br>

λμμ νμ§λ§ λ¬Έμ κ° νκ°λμ΄λ€. λ°°μ΄μ μ λ€λ¦­κ³Ό νΈνλμ§ μμΌλ λΉκ²μ¬ νλ³νμ μνν΄μΌ νκ³  κΉλν μ»΄νμΌλμ§ μμ κ²μ΄λ€.<br>
λ°λΌμ, μ΄κ±° νμμ ν€λ‘ μ¬μ©νλλ‘ μ€κ³ν μμ£Ό λΉ λ₯Έ Map κ΅¬νμ²΄κ° μ‘΄μ¬νλλ°, λ°λ‘ EnumMapμ΄λ€.<br>

<br>

* EnumMapμ μ¬μ©ν΄ λ°μ΄ν°μ μ΄κ±° νμμ λ§€ννλ€.
```java
Map<LifeCycle, Set<Plant>> plantsByLifeCycle = new EnumMap<>(Plant.LifeCycle.class);
for (Plant.LifeCycle lc : Plant.LifeCycle.values()) {
    plantsByLifeCycle.put(lc, new HashSet<>());
}
for (Plant p : garden) {
    plantsByLifeCycle.get(p.lifeCycle).add(p);
}
System.out.println(plantsByLifeCycle);
```
* μΆλ ₯ κ²°κ³Ό
  <br>
  <img width="40%" src="https://user-images.githubusercontent.com/55771326/168415762-8a62dfe8-67ec-4773-bf2b-b29d90aa554b.PNG">
  <br>

λ μ§§κ³  λͺλ£νκ³  μμ νκ³  μ±λ₯λ μλ λ²μ κ³Ό λΉλ±νλ€. μμ νμ§ μμ νλ³νμ μ°μ§ μκ³ , <br>
λ§΅μ ν€μΈ μ΄κ±° νμμ΄ κ·Έ μμ²΄λ‘ μΆλ ₯μ© λ¬Έμμ΄μ μ κ³΅νλ μΆλ ₯ κ΄γΉμ μ§μ  λ μ΄λΈμ λ¬ μΌλ μλ€.<br>

* μ€νΈλ¦Όμ μ¬μ©ν μ½λ
```java
System.out.println(Arrays.stream(garden).collect(groupingBy(p -> p.lifeCycle)));

System.out.println(Arrays.stream(garden).collect(groupingBy(p -> p.lifeCycle, () -> new EnumMap<>(LifeCycle.class), toSet())));
```
λ°°μ΄μ μΈλ±μ€λ₯Ό μ»κΈ° μν΄ ordinalμ μ°λ κ²μ μΌλ°μ μΌλ‘ μ’μ§ μμΌλ, λμ  EnumMapμ μ¬μ©νλΌ.<br>
λ€μ°¨μ κ΄κ³λ EnumMap<...,EnumMap<...>>μΌλ‘ νννλΌ. "μ νλ¦¬ μΌμ΄μ νλ‘κ·Έλλ¨Έλ Enum.ordinalμ (μ¬λ§ν΄μλ) μ¬μ©νμ§ λ§μμΌνλ€."<br>

<br>

## π―  μμ΄ν 38. νμ₯ν  μ μλ μ΄κ±° νμμ΄ νμνλ©΄ μΈν°νμ΄μ€λ₯Ό μ¬μ©νλΌ.
* μΈν°νμ΄μ€λ₯Ό μ΄μ©ν΄ νμ₯ κ°λ₯ μ΄κ±° νμμ νλ΄ λλ€.
```java
public interface Operation {
    double apply(double x, double y);
}
public enum BasicOperation implements Operation{
    PLUS("+") {
        public double apply(double x, double y) { return x + y; }
    },
    MINUS("-") {
        public double apply(double x, double y) { return x - y; }
    },
    TIMES("*") {
        public double apply(double x, double y) { return x * y; }
    },
    DIVIDE("/") {
        public double apply(double x, double y) { return x / y; }
    };

    private final String symbol;

    BasicOperation(String symbol) {
        this.symbol = symbol;
    }

    @Override public String toString() {
        return symbol;
    }
}
```
μ΄κ±° νμμ΄ κ·Έ μΈν°νμ΄μ€μ νμ€ κ΅¬νμ²΄ μ­ν μ νλ€. μ΄κ±° νμμΈ `BasicOperation`μ νμ₯ν  μ μμ§λ§ <br>
μΈν°νμ΄μ€μΈ `Operation`μ νμ₯ν  μ μκ³ , μ΄ μΈν°νμ΄μ€λ₯Ό μ°μ°μ νμμΌλ‘ μ¬μ©νλ©΄ λλ€.<br>

<br>

* νμ₯ κ°λ₯ μ΄κ±° νμ
```java
public enum ExtendedOperation implements Operation{
    EXP("^") {
        public double apply(double x, double y) {
            return Math.pow(x, y);
        }
    },
    REMAINDER("%") {
        public double apply(double x, double y) {
            return x % y;
        }
    };
    private final String symbol;
    ExtendedOperation(String symbol) {
        this.symbol = symbol;
    }
    @Override public String toString() {
        return symbol;
    }
}
```
μ΄κ±° νμ μμ²΄λ νμ₯ν  μ μμ§λ§, μΈν°νμ΄μ€μ κ·Έ μΈν°νμ΄μ€λ₯Ό κ΅¬ννλ κΈ°λ³Έ μ΄κ±° νμμ ν¨κ» μ¬μ©ν΄ κ°μ ν¨κ³Όλ₯Ό λΌ μ μλ€.<br>
μ΄λ κ² νλ©΄ ν΄λΌμ΄μΈνΈλ μ΄ μΈν°νμ΄μ€λ₯Ό κ΅¬νν΄ μμ λ§μ μ΄κ±° νμμ λ§λ€ μ μλ€.<br>

<br>

## π―  μμ΄ν 39. λͺλͺ ν¨ν΄λ³΄λ€ μ λνμ΄μμ μ¬μ©νλΌ.
λͺλͺ ν¨ν΄μ μ΄μ
1. μ€νκ° λλ©΄ μλλ€.
2. μ¬λ°λ₯Έ νλ‘κ·Έλ¨ μμμμλ§ μ¬μ©λλ¦¬λΌ λ³΄μ¦ ν  λ°©λ²μ΄ μλ€.
3. νλ‘κ·Έλ¨ μμλ₯Ό λ°°κ°λ³μλ‘ μ λ¬ν  λ§λν λ°©λ²μ΄ μλ€.<br>

<br>

μ΄λ¬ν λ¬Έμ λ₯Ό ν΄κ²°νκΈ° μν΄ μ λνμ΄μμ μ¬μ©νμ!
<br>

```java
@Aspect
@Component
public class SessionAspect {

    private static final String VO_SETTING_EXPRESSION = "execution(* com.inno.backoffice..*Mapper.insert*(..))"
                                                    + " || execution(* com.inno.backoffice..*Mapper.update*(..))"
                                                    + " || execution(* com.inno.backoffice..*Mapper.delete*(..))"
                                                    + " || execution(* com.inno.common..*Mapper.insert*(..))"
                                                    + " || execution(* com.inno.common..*Mapper.update*(..))"
                                                    + " || execution(* com.inno.common..*Mapper.delete*(..))";

    @Before(VO_SETTING_EXPRESSION)
    public void setVO(JoinPoint joinPoint) {
        Object[] objects = joinPoint.getArgs();
        if(SecurityContextHolder.getContext().getAuthentication()!= null) {
            InnoUser user = (InnoUser) SecurityContextHolder.getContext().getAuthentication().getPrincipal();
            AdminVO vo = user.getAdminVO();
            for (Object o : objects) {
                Method[] methods = o.getClass().getMethods();
                Optional<Method> setRegDtime = Arrays.stream(methods).filter(e -> e.getName().equals("setRegDate")).findFirst();
                Optional<Method> setModDtime = Arrays.stream(methods).filter(e -> e.getName().equals("setModDate")).findFirst();
                Optional<Method> setRegSn = Arrays.stream(methods).filter(e -> e.getName().equals("setRegSn")).findFirst();
                Optional<Method> setModSn = Arrays.stream(methods).filter(e -> e.getName().equals("setModSn")).findFirst();
                Optional<Method> getRegSn = Arrays.stream(methods).filter(e -> e.getName().equals("getRegSn")).findFirst();
                Optional<Method> getModSn = Arrays.stream(methods).filter(e -> e.getName().equals("getModSn")).findFirst();
                Date now = new Date();
                try {
                    if (joinPoint.getSignature().getName().startsWith("insert")) {
                        if (setRegDtime.isPresent()) {
                            setRegDtime.get().invoke(o, now);
                        }
                        if (setRegSn.isPresent()) {
                            if (CommonConstants.EMPTY.getValue().equals(StringUtil.null2void((String) getRegSn.get().invoke(o)))) {
                                setRegSn.get().invoke(o, vo.getAdminSn());
                            }
                        }
                    }
                    if (setModDtime.isPresent()) {
                        setModDtime.get().invoke(o, now);
                    }
                    if (setModSn.isPresent()) {
                        if (CommonConstants.EMPTY.getValue().equals(StringUtil.null2void((String) getModSn.get().invoke(o)))) {
                            setModSn.get().invoke(o, vo.getAdminSn());
                        }
                    }
                } catch (Exception e) {
                    //todo
                }
            }
        }
    }
}
```
μ μμ€λ λ΄κ° AOPλ₯Ό νμ©νμ¬ λ±λ‘μ, λ±λ‘λ μ§, μμ μ, μμ λ μ§λ₯Ό setν΄μ£Όλ κΈ°λ₯μ΄λ€.<br>
νμ§λ§ μ μμ€μ `VO_SETTING_EXPRESSION`μ ν΅ν΄ λͺλͺ ν¨ν΄μ μ¬μ©νμκΈ° λλ¬Έμ μμ μ€λͺν μ΄μλ€μ κ°κ³  μλ€.<br>
λ°λΌμ, ` @Before("@annotation(μ»€μ€ν μ λνμ΄μ)")`μ μ¬μ©νμ¬ λͺλͺν¨ν΄μ ννΌν΄μΌ λ  κ² κ°λ€.

<br>

## π―  μμ΄ν 40. @Override μ λνμ΄μμ μΌκ΄λκ² μ¬μ©νλΌ.
μ¬μ μν λͺ¨λ  λ©μλμ `@Override` μ λνμ΄μμ μμμ μΌλ‘ λ¬λ©΄ μ€μ νμλ μ»΄νμΌλ¦¬κ° μλ €μ€ κ²μ΄λ€.<br>
μμΈλ ν κ°μ§λΏμ΄λ€. κ΅¬μ²΄ ν΄λμ€μμ μμ ν΄λμ€μ μΆμ λ©μλλ₯Ό μ¬μ μν κ²½μ°μ μ΄ μ λνμ΄μμ λ¬μ§ μμλ λλ€.<br>

<br>

## π―  μμ΄ν 41. μ μνλ €λ κ²μ΄ νμμ΄λΌλ©΄ λ§μ»€ μΈν°νμ΄μ€λ₯Ό μ¬μ©νλΌ.

λ§μ»€ μΈν°νμ΄μ€μ λ§μ»€ μ λνμ΄μμ κ°μμ μ°μμ΄ μλ€. μλ‘ μΆκ°νλ λ©μλ μμ΄ λ¨μ§ νμ μ μκ° λͺ©μ μ΄λΌλ©΄ λ§μ»€ μΈν°νμ΄μ€λ₯Ό μ ννμ.<br>
ν΄λμ€λ μΈν°νμ΄μ€ μΈμ νλ‘κ·Έλ¨ μμμ λ§νΉν΄μΌ νκ±°λ, μ λνμ΄μμ μ κ·Ή νμ©νλ νλ μμν¬μ μΌλΆλ‘ κ·Έ λ§μ»€λ₯Ό νΈμμν€κ³ μ νλ€λ©΄ <br>
λ§μ»€ μ λνμ΄μμ΄ μ¬λ°λ₯Έ μ νμ΄λ€. λ°λΌμ `ElementType.TYPE`μ μ¬μ©ν κ±°λΌλ©΄ νλ² λ μκ°ν΄λ³΄μ.<br>

<br>

> Joshua Bloch, γEffective Java 3/Eγ, κ°μλ§΅μ μ?κΉ, νλ‘κ·Έλλ°μΈμ¬μ΄νΈ(2018), p207-251.