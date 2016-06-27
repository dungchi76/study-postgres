# study-postgres

### PostgreSQL Query

* ***사용자 생성***

  ```
  > createuser -P username
  
  Enter password for new user: 
  Enter it again: 
  Shall the new user be allowed to create databases? (y/n) n
  Shall the new user be allowed to create more new users? (y/n) n
  CREATE USER
  ```

* ***DB 생성***

  - Simple 예제
  ```
  > createdb -O username dbname
  
  CREATE DATABASE
  ```
  
  - Full 예제 (Owner, Encoding, Tablespace, Comment, ... 지정 )
  ```
  CREATE DATABASE dbname
    WITH OWNER = username
       ENCODING = 'UTF8'
       TABLESPACE = pg_default
       LC_COLLATE = 'en_US.utf8'
       LC_CTYPE = 'en_US.utf8'
       CONNECTION LIMIT = -1;

  COMMENT ON DATABASE postgres IS 'default administrative connection database';
  ```

* ***Table 생성***

  ```
  CREATE TABLE public.table_name
  (
    key          char(16) PRIMARY KEY,
    val1        integer not null,
    val2        integer unique,
    val3        integer default 0 not null
  )
  ``` 

  - constraint으로 Primary Key를 설정하거나, 복수의 Primary Key는 아래와 같이 설정
  ```
  CREATE TABLE public.table_name
  (
    key          char(16),
    val1        integer,
    val2        integer,
    CONSTRAINT {primary_key_name} PRIMARY KEY ( {colmun1}, {colmun2} )
  )
  ```

* ***기본 Data Type***

  | Data Type                         |Description  |
  |-----------------------------------|-------------|
  | smailint                          |2 바이트 정수|
  | integer                           |4 바이트 정수|
  | bigint                            | 8 바이트 정수|
  | decimal(a, a)/numeric(a, s)       | 10진수형    |
  | real                              | 6자리 단정도 부동소수점|
  | double precision                  | 15 자리 배정도 부동소수점|
  | serial                            | 4 바이트 일련번호|
  | bigserial                         | 8 바이트 일련번호|
  | date                              | 일자        |
  | time                              | 시간        |
  | timestamp                         | 일자시간    |
  | char(문자수)/character            |고정길이 문자열  (최대 4096 문자)|
  | varchar(문자수)/charcter varying  |가변길이 문자열  (최대 4096 문자)|
  | text                              |무제한 텍스트|
  | Large Object                      |oid형        |
  | boolean/bool                      |true/false   | 


* ***Key 만들기***

  - 단일 primary 키 : 설정할 필드에 primary key를 지정.

  ```
  ...
    key          char(16)     primary key,
  ...
  ```

  - 복수 primary 키: CONSTRAINT를 이용해서 지정
  ```
  CREATE TABLE test_db (
    key         char(16),
    val1        integer,
    val2        integer,
    CONSTRAINT pk_name PRIMARY KEY (
      key,
      val1
    )
  );
  ```
  
  - Index 추가
  
  ```
  create unique index PK_NAME on test_db (
    key,
    val1
  )
  ```
  
  - Key 삭제
  
  ```
  drop index PK_NAME;
  ```
  
* ***자동 증가 만들기***

  - serial: Column의 자동 증가는가 data type을 serial로 간단히 정의
  ```
  create table testa (
    key          char(16),
    val1        serial,
    val2        integer,
  );
  ```

  ```
  insert into testa values('key00A');
  insert into testa values('key00B');

  key      | val1 | val2 
  ---------+------+------
  key00A   |    1 |      
  key00B   |    2 | 
  ```

* ***Sequence 만들기***

  - Sequence: 다수의 테이블 및 사용자 임의 증가 시 이용
  ```
  create sequence seq;
  ```
  
  - 다음과 같이 만들면 10 ～ 1000000, 10씩 증가하며 최대값이 되면 다시 최소값으로 돌아간다
  ```
  create sequence seq
    increment    10
    minvalue     10
    maxvalue     1000000
    start        10
    cache        100
    cycle
  ;
  ```

  - 사용 법
    - nextval(~): 일련 번호 증가
    - curval(~): 현재 값 가져옴
  
  ```
  insert into testa values ('k001', nextval('seq'), 10);
  insert into testa values ('k002', nextval('seq'), 10);
  insert into testa values ('k003', currval('seq'), 10);

   key    | val1 | val2 
  --------+------+------
  k001    | 10   |   10 
  k002    | 20   |   10 
  k003    | 20   |   10
  ```

  
---------------------------------------------------------------------------------------

### Oracle / PostgresSQL / MySQL - Data Type 비교

|Oracle |PostgreSQL        |MySQL        |Description                        |
|:------|:-----------------|:------------|:----------------------------------|
|int [] |bigint, int8      |bigint       |signed eight-byte integer          |
|       |bigserial, serial8|             |autoincrementing eight-byte integer|
|       |bit[]             |             |fixed-length bit string            |
|       |bit varying[], varbit|          |variable-length bit string         |
|       |boolean, bool     |boolean, bool|logical Boolean (true/false)       |
|       |box               |             |rectangular box in the plane       |
|       |bytea             |             |binary data ("byte array")         |
|varchar2| character varying[], varchar[]|varchar|variable-length character string|
|char   |character[], char[]|char        |fixed-length character string      |
|       |cidr              |             |IPv4 or IPv6 network address       |
|       |circle            |             |circle in the plane                |
|       |date              |             |calendar date (year, month, day)   |
|       |double precision,float8|        |double precision floating-point number|
|       |                  |enum         |enumeration type, max 65,535 elements|
|       |inet              |             |IPv4 or IPv6 host address          |
|int[]  |integer,int,in4   |int          |signed four-byte integer           |
|       |interval[p]       |             |time span                          |
|       |line              |             |infinite line in the plane         |
|       |lseg              |             |line segment in the plane          |
|       |macaddr           |             |MAC address                        |
|       |money             |             |currency amount                    |
|number |numeric[p,s],decimal[p,s]|int   |exact numeric of selectable precision|
|       |path              |             |geometric path in the plane        |
|       |point             |             |geometric point in the plane       |
|       |polygon           |             |closed geometric path in the plane |
|       |real,float4       |             |single precision floating-point number|
|int [] |smallint,int2     |smallint     |signed two-byte integer            |
|       |serial,serial4    |             |autoincrementing four-byte integer |
|blob, cblob|text          |blob, text   |variable-length character string   |
|       |time [p] [without time zone]|time|time of day                       |
|       |time [p] with time zone, timetz| |time of day, including time zone  |
|       |timestamp [p] [without time zone]|timestamp|date and time           |
|       |timestamp [p] with time zone, timestamptz| |date and time, inc      |
