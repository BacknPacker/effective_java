# 2장. 객체 생성과 파괴

## 아이템2. 생성자에 매개변수가 많다면 필터를 고려하라.
<br>

#### 이전 코드
+ Spring 프레임워크에서 Lombok을 사용한 엔티티
```java
@Getter
@NoArgsConstructor
@Entity
public class Scheduler{
  @Id
  @GeneratedValue(strategy= GenerationType.IDENTITY)
  private Long schedule_no;

  @Column(columnDefinition = "TEXT default ''", length=600)
  private String schedule_attender;

  @Column(columnDefinition = "TEXT", length=100, nullable = false)
  private String teacher_id;
  private String schedule_date;
  private String schedule_start;
  private String schedule_end;
  private String schedule_space;

  @Builder
  public Scheduler(Long schedule_no, String schedule_attender, String teacher_id, String schedule_date, String schedule_start,
      String schedule_end, String schedule_space){
    this.schedule_no = schedule_no;
    this.schedule_attender = schedule_attender;
    this.teacher_id = teacher_id;
    this.schedule_date = schedule_date;   /* 형식: 2021-12-10 */
    this.schedule_start = schedule_start;
    this.schedule_end = schedule_end;
    this.schedule_space = schedule_space;
  }
}
```
<br>

#### 변경 후 코드
+ @Builder 부분 수정
```java
@Getter
@NoArgsConstructor
@Entity
@Builder(builderMethodName="scheduleDtoBuilder")
public class Scheduler{
  @Id
  @GeneratedValue(strategy= GenerationType.IDENTITY)
  private Long schedule_no;

  @Column(columnDefinition = "TEXT default ''", length=600)
  private String schedule_attender;

  @Column(columnDefinition = "TEXT", length=100, nullable = false)
  private String teacher_id;
  @Builer.Default private String schedule_date = "2022-04-17" // 현재 날짜 값;
  @Builer.Default private String schedule_start = "11:00" // 현재 시간 값;
  @Builer.Default private String schedule_end = "11:00" // 현재 시간 값;
  @Builer.Default private String schedule_space = "5";
  
  public static Scheduler builder(Long schedule_no, String schedule_attender, String teacher_id){
    return scheduleDtoBuilder()
        .schedule_no(schedule_no)
        .schedule_attender(schedule_attender)
        .teacher_id(teacher_id);
  }
}
```
<br>

#### 코드리뷰 정리
+ 초기값을 설정으로 불안정 객체 생성방지
+ @Builder 위치 변경으로 정적 팩터리 메소드에 의한 객체 생성 