# [SQL] PostgreSQL

```sql
CREATE TABLE EMP ( 
    NoEMP NUMERIC(4), -- NUMERIC, DECIMAL 타입으로 integer를 표현할 수 있다. PRIMARY KEY를 바로 써줄수도 있어
    NomEMP VARCHAR(10), -- 10자리 까지의 string일 경우
    EMPLOI VARCHAR(15), 
    MGR NUMERIC(4), --4자리 까지의 integer일 경우
    DateEMB DATE, -- 날짜
    SAL DECIMAL(7, 2),
    COMM DECIMAL(7, 2),
    NoDEPT NUMERIC(2),
    CONSTRAINT emp_clé_prim PRIMARY KEY (NoEmp) 
    -- 처음에 같이 PRIMARY KEY를 선언할 수 있지만, 이렇게 마지막에 추가해줄 수도 있다. 
);
```

```sql
insert into table_name (column1, column2, ... ) values (data1, data2, ... )
--날짜 타입의 데이터를 어떻게 삽입하는지 유심히 보자 
INSERT INTO EMP VALUES (7369, 'SERGE', 'FONCTIONNAIRE', 7902, TO_DATE('17-DEC- 1980', 'DD-MON-YYYY'), 800, NULL, 20);
```

```sql
ALTER TABLE EMP
ADD CONSTRAINT emp_clé_etr FOREIGN KEY (NoDEPT) REFERENCES DEPT(NoDept);
-- 외래키를 깜빡했으면 ALTER를 이용하여 넣어줄 수 있다. 

ALTER TABLE DEPT
ADD COLUMN EXEMPLE VARCHAR(10);
-- 물론 COLUMN도 넣어줄 수 있고 

ALTER TABLE EMP
RENAME COLUMN COMM TO BONUS;
-- RENAME도 가능 
```

```sql
SELECT NomDEPT FROM DEPT WHERE LOC LIKE 'R%' OR LOC LIKE 'D%';
-- LOC에서 R 이나 D로 시작하는거  

SELECT NomDEPT FROM DEPT WHERE LOC LIKE '%R%' OR LOC LIKE '%D%';
-- LOC에서 R 이나 D가 포함되어 있는거 
```

```sql
SELECT NomEMP, EMPLOI, SAL, NoDEPT FROM EMP ORDER BY NoDEPT DESC, SAL ASC;
-- ORDER BY 다음에는 AND 쓸 필요 없이 컴마(,)를 이용할 수 있다. 
```

```sql
UPDATE EMP SET SAL = SAL + 200; 
-- UPDATE 모듭 값 한번에 최신화 가능 
```

```sql
DELETE FROM COMMISSION; 
-- 데이터 전부 삭제 
DROP TABLE COMMISSION;
-- 테이블 삭제
```

```sql
SELECT 
    COUNT(*)
FROM 
    EMP
WHERE
    SAL > ALL (
        SELECT 
            MIN(SAL)
        FROM 
            EMP 
    );

SELECT 
    NomEMP
FROM 
    EMP
WHERE
    SAL > ALL (
        SELECT 
            MAX(SAL)
        FROM
            EMP
        WHERE
            NoDEPT = 20
    ) AND 
        NoDEPT = 10;

-- 이렇게도 sql를 사용할 수 있다. 
```

```sql
SELECT
    MIN(SAL),
    MAX(SAL)
FROM 
    EMP
GROUP BY 
    NoDEPT
HAVING
    COUNT(DISTINCT EMPLOI) >= 2;
-- GROUP BY 절과 함께 HAVING절을 사용하여 GROUP BY의 결과를 특정 조건으로 필터링 하는 기능을 한다.
-- 예로들어
-- PAYMENT 테이블을 CUSTOMER_ID 기준으로 GROUP BY 하고 SUM(AMOUNT) 가 200을 초과하는 값을 조회한다면

SELECT
	CUSTOMER_ID,
	SUM(AMOUNT) AS AMOUNT_SUM
FROM
	PAYMENT
GROUP BY
	CUSTOMER_ID
HAVING
	SUM(AMOUNT) > 200;
```

## References

[PostgreSQL Tutorial](https://www.postgresqltutorial.com)

[PostgreSQL/데이터 조회와 필터링](https://dog-developers.tistory.com/category/PostgreSQL/데이터%20조회와%20필터링)