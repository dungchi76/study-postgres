
### 날짜 관련 함수 및 날짜 연산

 - 오늘 (date)
 ```sql
 select current_date;
 
 "2016-06-29"
 ```

 - 현재시각 (timestamp)
 ```sql
 select now(),  
 select current_timestamp;
 
 "2016-06-29 04:58:57.974302+00"
 "2016-06-29 04:58:57.974302+00"
 ```


 - 어제/오늘/내일
 ```sql
 select
  current_date - 1 "어제",
  current_date     "오늘",
  current_date + 1 "내일";
   
 "2016-06-28", "2016-06-29", "2016-06-30"
 ```

 - Day of Week (일요일(0) ~ 토요일(6))

 ```sql
 select extract(dow from current_date);
  
 3
 ```

 - Day of Year
 ```sql
 select extract(doy from current_date);
 
 181
 ```

 - Week of Year
 ```sql
 select extract(week from current_date);
 
 26
 ```
 
 - 두 날짜 사이의 날수
 ```sql
 select '2016-06-05'::date - '2016-06-25'::date;
 
 10
 ```

### 한 주의 첫날, 마지막 날 구하기

 - 기본적으로 첫 날을 월요일로 잡음. (일~금요일로 바꾸러면 -1 해줘야 함) 
 - date_trunc() 함수의 리턴 타입은 timestamp임

 - 이번 주
 ```sql
 select
   date_trunc('week', current_date)::date - 1      "이번 주 첫날 (일요일)",
   date_trunc('week', current_date)::date + 5      "이번 주 마지막 날(금요일)"
 ;
  
  "2016-06-26", "2016-07-02"
  ```
 
 - 전 주
 ```sql
 select
   date_trunc('week', current_date - 7)::date - 1     "전 주 첫날",
   date_trunc('week', current_date - 7)::date + 5  "전 주 마지막 날"
 ;
 
 "2016-06-19", "2016-06-25"
 ```

 - 다음 주
 ```sql
 select
   date_trunc('week', current_date + 7)::date - 1     "다음 주 첫날",
   date_trunc('week', current_date + 7)::date + 5  "다음주 마지막 날"
 ;
 ```

 * (주 첫 날을 일요일로 할 때) 주 첫날/마지막 날
 * week로 date_trunc를 하는 경우 결과가 월요일 날짜가 되기 때문에
 * 한 주를 일요일~토요일까지로 하는 경우는 -1 필요

 - 이번 주
 ```sql 
 select
   date_trunc('week', current_date)::date - 1         "이번 주 첫날",
   date_trunc('week', current_date)::date + 6 - 1     "이번 주 마지막 날"
 ;
 ```

 - 전 주
 ```sql
 select
   date_trunc('week', current_date - 7)::date - 1     "전 주 첫날",
   date_trunc('week', current_date - 7)::date + 6 - 1 "전주 마지막 날"
 ;
 
 "2016-06-19";"2016-06-25"
 ```

 - 다음 주
 ```sql
 select
   date_trunc('week', current_date + 7)::date - 1     "다음 주 첫날",
   date_trunc('week', current_date + 7)::date + 6 - 1 "다음주 마지막 날"
 ;
 
 "2016-07-03", "2016-07-09"
 ```


### 달의 첫 날, 마지막 날

 - 한 달 전/후 날짜
 ```sql
 select
   current_date - interval '1 months' "전 달",
   current_date + interval '1 months' "다음 달"
   
  "2016-05-29 00:00:00", "2016-07-29 00:00:00"
  ;
  ```
 
 - 이번 달 첫날, 마지막 날
 ```sql
 select
   date_trunc('month', current_date)::date "첫날",
   date_trunc('month', current_date + interval '1 months')::date - 1 "마지막 날"
 ;
 ```
 
 - 전달 첫날, 마지막 날
 ```sql
 select
   date_trunc('month', current_date - interval '1 months')::date "첫 날",
   date_trunc('month', current_date)::date - 1 "마지막 날"
 ;
 
 "2016-05-01", "2016-05-31"
 ```

 - 다음 달 첫날, 마지막 날
 ```sql
 select
   date_trunc('month', current_date + interval '1 months')::date "첫 날",
   date_trunc('month', current_date + interval '2 months')::date - 1 "마지막 날"
 ;
 ```

### 이번 주 첫날, 마지막 날

 - 이번 주 날짜
 ```sql
 select
   date_trunc('week', current_date)::date -1 + i "일~토"
 from generate_series(0,6) as t(i);
 
 "2016-06-26"
 "2016-06-27"
 "2016-06-28"
 "2016-06-29"
 "2016-06-30"
 "2016-07-01"
 "2016-07-02"
 ```


### 이번달 첫날 ~ 마지막 날 (1~30)

 -- 이번 달 날짜 (첫날 ~ 마지막 날)
 ```sql
 select date_trunc('month', current_date)::date + (i - 1)
 from
   generate_series(
     1,
     extract(day from date_trunc('month', current_date + interval '1 months')::date - 1)::integer
   ) as t(i);
 ```
 또는
 ```sql
 select date_trunc('month', current_date)::date + (i - 1)
 from
   generate_series(
     1,
     extract(day from date_trunc('month', current_date) + interval '1 months' - interval '1 days')::integer
   ) as t(i);
 ```
 결과
 ```
 "2016-06-01"
 "2016-06-02"
 ...
 "2016-06-30"
 ```

### 이번 달 (첫날 ~ 마지막날) & Week of Month

 ```sql
 select dt, to_char(dt, 'W') "day of week"
 from (
     select date_trunc('month', current_date)::date + i dt
     from generate_series(0, 30) as t(i)
     ) t
 where extract(month from dt) = extract(month from current_date)
 ;
 
 "2016-06-01";"1"
 "2016-06-02";"1"
 ...
 "2016-06-30";"5"
 ```


