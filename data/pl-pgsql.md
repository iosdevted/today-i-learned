# [SQL] PL/pgSQL

이 글은 Stored Procedure에 대한 강좌 형식으로 정리하는 글입니다. PostgreSQL는 프로그래밍 언어를 이용해 함수를 직접 만들어 실행하기 위해 PL/pgSQL이라는 언어를 제공합니다. 이 PL/pgSQL를 활용한다는 것이 바로 Stored Procedure를 사용한다는 것이고, 이를 이용해 Trigger를 사용할 수 있는 발판이 됩니다.

PL/pgSQL은 PostgreSQL을 보다 편리하고 빠른 응답성을 제공받기 위해 사용할 수 있을 뿐만 아니라 데이터베이스 자체의 기능을 확장할 수 있는 기반을 제공합니다. PostgreSQL의 Stored Procedure 개발을 위한 언어가 비단 PL/pgSQL만 가능한 것은 아닌데요, 특히 C언어를 통해서도 개발이 가능하지만, 이 글에서는 PL/pgSQL에만 집중하도록 하겠습니다.

```sql
CREATE FUNCTION function_name(param1 type, param2 type)
 RETURNS return_type AS
BEGIN
 -- code
END;
LANGUAGE language_name;

CREATE FUNCTION add(a INTEGER, b INTEGER)
RETURNS INTEGER AS
$$ BEGIN
    RETURN a+b;
END; $$
LANGUAGE PLPGSQL;
```

위의 코드 중 CREATE FUNCTION 코드 대신 CREATE OR REPLACE FUNCTION 코드로 대체하면 아래와 같다.

```sql
CREATE OR REPLACE FUNCTION add(a INTEGER, b INTEGER)
RETURNS INTEGER AS
$$ BEGIN
    RETURN a+b;
END; $$
LANGUAGE PLPGSQL;
```

위의 CREATE OR REPLACE FUNCTION 코드는 CREATE FUNCTION와 다르게 이미 동일한 함수가 존재할 경우 함수를 새롭게 교체하라는 의미를 갖습니다. 이 add 함수는 정수형 타입인 a와 b 인자를 갖습니다. 기본적으로 인자는 IN 형식입니다. IN 형식 이외에 OUT과 INOUT 형식의 인자도 지정이 가능합니다. IN 형식으로 지정된 인자는 단지 함수에 값만을 전달해 주는 목적이고, OUT은 함수 종료시 이 인자에 값을 전달해 외부에 반환해주는 목적도 갖습니다. INOUT은 IN과 OUT에 대한 목적을 모두 갖습니다.

```sql
CREATE OR REPLACE FUNCTION getmaxmin(
    v1 NUMERIC, 
    v2 NUMERIC,
    OUT min_value NUMERIC,
    OUT max_value NUMERIC)
AS 
$$ BEGIN
    min_value := LEAST(v1, v2);
    max_value := GREATEST(v1, v2);
END; $$
LANGUAGE PLPGSQL;

SELECT * FROM getmaxmin(2017, 2021);
```

위의 getmaxmin이라는 함수는 4개의 인자를 받습니다. 첫번째와 두번째 인자는 별도의 지정이 없으므로 IN 형식이고 min_value와 max_value는 OUT 형식으로 지정하였습니다. 이 함수는 첫번째와 두번째로 지정된 인자값중 최대값을 max_value의 인자로 넘기고 최소값은 min_value의 인자로 넘깁니다. 이 함수를 보면 별도의 반환값을 지정하기 위한 RETURNS 구문을 사용하지 않고, 이처럼 OUT 형식으로 지정된 인자에 값을 반환하고 있습니다.

다음으로 INOUT 형식은 IN과 OUT에 대한 특성 모드를 갖습니다. 즉, 값은 함수로 전달할 수도 있고 함수 내부에서 값을 변경해 외부로 값을 전달도 할 수 있습니다. 아래의 함수를 예로 살펴보겠습니다.

```sql
CREATE OR REPLACE FUNCTION getmaxmin2(
    INOUT v1 NUMERIC, 
    INOUT v2 NUMERIC)
AS 
$$ DECLARE
    v3 NUMERIC := v1;
BEGIN
    v1 := GREATEST(v2, v3);
    v2 := LEAST(v2, v3);
END; $$
LANGUAGE PLPGSQL;
```

IN, OUT, INOUT 형식 이외에도 성격은 다르지만 VARIADIC 형식이 있습니다. 이 VARIADIC 형식은 가변인자로써 동일한 데이터 타입의 여러개의 인자들을 한번에 지정할 수 있습니다. 다음의 예를 살펴보겠습니다.

```sql
CREATE OR REPLACE FUNCTION sum(VARIADIC params numeric[]) 
RETURNS numeric AS 
$$ DECLARE
    res numeric := 0;
BEGIN
    FOR i IN 1 .. array_length(params, 1) LOOP
        res := res + params[i];
    END LOOP;
    RETURN res;
END; $$ 
LANGUAGE plpgsql;
```

위의 코드에서 params 인자는 VARIADIC 형식이고 타입은 NUMERIC[]입니다. 지정한 인자의 개수는 array_length 내장 함수를 사용해 얻을 수 있습니다. FOR 반복만을 사용하고 있는데, 1부터 인자의 개수만큼 반복하고 있습니다. 반복문에 대해서는 추후 자세히 설명하겠습니다. 이 함수는 여러개의 숫자값들로 인자를 지정하여 그 합한 결과를 반환하는데요. 이 함수의 실행 결과의 예는 아래와 같습니다.

```sql
SELECT sum(1, 2, 3, 4, 5, 6, 7, 8, 9)
```

변수란 어떤 값을 가지고 있으면서, 필요할 경우 그 값을 변경할 수 있습니다. 또한 변수는 그 이름과 변수가 가지는 값의 타입을 지정해야 합니다.

변수의 이름은 개발자에게 의미있는 이름을 부여하는 것이 좋고, 값의 타입은 integer, numeric, char, varchar 등과 같이 각각 정수, 실수, 문자, 가변길이 문자 등으로 지정할 수 있습니다.

변수는 선언 시에 기본값을 지정할 수 있습니다. 만약 지정하지 않는다면 그 변수의 값은 NULL 값을 갖게 됩니다.

```sql
DO $$ 
DECLARE
   age integer := 40;
   korean_name varchar(10) := '김형준';
   alias_name varchar(50) := 'Dip2K';
   weight numeric(3,1) := 65.5;
BEGIN 
   RAISE NOTICE '%의 별명은 % 이고 나이는 %이며 몸무게는 %입니다.', 
       korean_name, alias_name, age, weight;
END $$;
```

RAISE NOTICE는 해당 변수들을 원하는 문자열로 포맷으로 구성한 것을 PL/pgSQL의 실행환경에서 표시하는 코드입니다.

상수는 변수와 다르게 한번 값이 정해지면 변경할 수 없다는 차이가 있습니다. 상수를 선언하는 방식은 변수의 선언처럼 DECLARE 구문 뒤에서 이루어지며, 상수 이름과 타입 사이에 CONSTANT라고 지정하면 됩니다. 아래의 예를 보면 쉽게 알 수 있습니다.

```sql
DO $$ 
DECLARE
   vat CONSTANT numeric := 0.1;
   cost numeric := 100000;
   real_cost numeric;
BEGIN 
   real_cost = cost + cost * vat;
   RAISE NOTICE '부가세를 적용한 실제 가격은 % 입니다', real_cost;
END $$;
```

조건문은 어떤 조건에 대해 실행할 코드를 분리하기 위한 목적으로 사용됩니다.

```sql
DO $$
DECLARE
    a integer := 20;
    b integer := 40;
BEGIN 
    IF a > b THEN
        RAISE NOTICE 'a가 b보다 더 큽니다.';
    ELSEIF a = b THEN
        RAISE NOTICE 'a와 b가 같습니다.';
    ELSE
        RAISE NOTICE 'a와 b가 같지 않고, a가 b보다 더 크지도 않습니다.';
    END IF;
END $$;
```

## References

[PostgreSQL의 PL/pgSQL 튜토리얼](http://www.gisdeveloper.co.kr/?p=4546)