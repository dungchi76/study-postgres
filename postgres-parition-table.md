
# PostgreSQL Partition Table (작업 중)


```sql
CREATE TABLE p_emp
(
  empno integer NOT NULL,
  ename character varying(10),
  job character varying(9),
  mgr integer,
  hiredate date,
  sal integer,
  comm integer,
  deptno integer,
  CONSTRAINT p_emp_pkey PRIMARY KEY (empno)
);

CREATE TABLE p_emp_old (
CHECK (hiredate < DATE '2011-01-01')
) INHERITS (p_emp);


CREATE TABLE p_emp_2011 (
CHECK (hiredate >= DATE '2011-01-01' and hiredate < DATE '2012-01-01')
) INHERITS (p_emp);

CREATE TABLE p_emp_2012 (
CHECK (hiredate >= DATE '2012-01-01' and hiredate < DATE '2013-01-01')
) INHERITS (p_emp);

CREATE TABLE p_emp_2013 (
CHECK (hiredate >= DATE '2013-01-01' and hiredate < DATE '2014-01-01')
) INHERITS (p_emp);

CREATE TABLE p_emp_last (
CHECK (hiredate >= DATE '2014-01-01')
) INHERITS (p_emp);

ALTER TABLE p_emp_old ADD CONSTRAINT p_emp_old_pkey PRIMARY KEY (empno);
ALTER TABLE p_emp_2011 ADD CONSTRAINT p_emp_2011_pkey PRIMARY KEY (empno);
ALTER TABLE p_emp_2012 ADD CONSTRAINT p_emp_2012_pkey PRIMARY KEY (empno);
ALTER TABLE p_emp_2013 ADD CONSTRAINT p_emp_2013_pkey PRIMARY KEY (empno);
ALTER TABLE p_emp_last ADD CONSTRAINT p_emp_last_pkey PRIMARY KEY (empno);

CREATE INDEX p_emp_ix01 ON p_emp(deptno);
CREATE INDEX p_emp_old_ix01 ON p_emp_old(deptno);
CREATE INDEX p_emp_2011_ix01 ON p_emp_2011(deptno);
CREATE INDEX p_emp_2012_ix01 ON p_emp_2012(deptno);
CREATE INDEX p_emp_2013_ix01 ON p_emp_2013(deptno);
CREATE INDEX p_emp_2014_ix01 ON p_emp_last(deptno);

CREATE OR REPLACE FUNCTION p_emp_func_insert_trigger()
RETURNS TRIGGER AS $$
BEGIN
    IF ( NEW.hiredate < DATE '2011-01-01') THEN
        INSERT INTO p_emp_old VALUES (NEW.*);
    ELSIF ( NEW.hiredate >= DATE '2011-01-01' and NEW.hiredate < DATE '2012-01-01') THEN
        INSERT INTO P_emp_2011 VALUES (NEW.*);
    ELSIF ( NEW.hiredate >= DATE '2012-01-01' and NEW.hiredate < DATE '2013-01-01') THEN
        INSERT INTO P_emp_2012 VALUES (NEW.*);
    ELSIF ( NEW.hiredate >= DATE '2013-01-01' and NEW.hiredate < DATE '2014-01-01') THEN
        INSERT INTO P_emp_2013 VALUES (NEW.*);
    ELSE
        INSERT INTO P_emp_last VALUES (NEW.*);
    --ELSE
        --RAISE EXCEPTION 'Date out of range.  Fix the measurement_insert_trigger() function!';
    END IF;
    RETURN NULL;
END;
$$
LANGUAGE plpgsql;

CREATE TRIGGER trigger_p_emp_insert
    BEFORE INSERT ON p_emp
    FOR EACH ROW EXECUTE PROCEDURE p_emp_func_insert_trigger();

INSERT INTO P_EMP VALUES
        (7369, 'SMITH',  'CLERK',     7902,
        TO_DATE('17-DEC-2010', 'DD-MON-YYYY'),  800, NULL, 20);
INSERT INTO P_EMP VALUES
        (7499, 'ALLEN',  'SALESMAN',  7698,
        TO_DATE('20-FEB-2011', 'DD-MON-YYYY'), 1600,  300, 30);
INSERT INTO P_EMP VALUES
        (7521, 'WARD',   'SALESMAN',  7698,
        TO_DATE('22-FEB-2014', 'DD-MON-YYYY'), 1250,  500, 30);
```


```
scottdb=# select * from p_emp;
 empno | ename |   job    | mgr  |  hiredate  | sal  | comm | deptno
-------+-------+----------+------+------------+------+------+--------
  7499 | ALLEN | SALESMAN | 7698 | 2011-02-20 | 1600 |  300 |     30
  7369 | SMITH | CLERK    | 7902 | 2010-12-17 |  800 |      |     20
  7521 | WARD  | SALESMAN | 7698 | 2014-02-22 | 1250 |  500 |     30
(3 rows)


scottdb=# select * from p_emp_old;
 empno | ename |  job  | mgr  |  hiredate  | sal | comm | deptno
-------+-------+-------+------+------------+-----+------+--------
  7369 | SMITH | CLERK | 7902 | 2010-12-17 | 800 |      |     20
(1 row)


scottdb=# select * from p_emp_2011;
 empno | ename |   job    | mgr  |  hiredate  | sal  | comm | deptno
-------+-------+----------+------+------------+------+------+--------
  7499 | ALLEN | SALESMAN | 7698 | 2011-02-20 | 1600 |  300 |     30
(1 row)


scottdb=# select * from p_emp_2012;
 empno | ename | job | mgr | hiredate | sal | comm | deptno
-------+-------+-----+-----+----------+-----+------+--------
(0 rows)


scottdb=# select * from p_emp_2013;
 empno | ename | job | mgr | hiredate | sal | comm | deptno
-------+-------+-----+-----+----------+-----+------+--------
(0 rows)


scottdb=# select * from p_emp_last;
 empno | ename |   job    | mgr  |  hiredate  | sal  | comm | deptno
-------+-------+----------+------+------------+------+------+--------
  7521 | WARD  | SALESMAN | 7698 | 2014-02-22 | 1250 |  500 |     30
(1 row)
```
