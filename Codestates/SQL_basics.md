### **SQL 명령어 간략하게 살펴보기**

위에서 명시된 명령어들 중에서 자주 사용되는 명령어 몇 가지만 설명을 해보겠습니다.

### ***SELECT***

선택이라는 단어처럼 데이터셋에 포함될 특성들을 특정합니다.

- 일반 문자열

```
SELECT 'hello world';
```

- 숫자

```
SELECT 2;
```

- 간단한 연산

```
SELECT 15 + 3;
```

### ***FROM***

테이블과 관련이 있는 경우 필수로 명시해야 하는 명령어 입니다. 결과들을 도출해낼 데이터베이스 테이블을 명시합니다.

- 특정 특성을 테이블에서 사용

```
SELECT 특성_1
FROM 테이블_이름;

-- 예시
SELECT FirstName
FROM customers;
```

- 특정 특성들을 테이블에서 사용

```
SELECT 특성_1, 특성_2
FROM 테이블_이름;

-- 예시
SELECT customers.FirstName, customers.LastName
FROM customers;
```

- 테이블의 모든 특성을 선택

```
SELECT *
FROM 테이블_이름;

-- 예시
SELECT *
FROM customers;
```

> * 는 와일드카드 (wildcard) 로 전부 선택할 때에 사용됩니다.

### ***WHERE***

(선택적으로) 필터 역할을 하는 쿼리문입니다.

- 특정 값과 동일한 데이터 찾기

```
SELECT 특성_1, 특성_2
FROM 테이블_이름
WHERE 특성_1 = "특정 값";

-- 예시
SELECT customers.FirstName, customers.LastName
FROM customers
WHERE customers.FirstName = 'Helena';
```

- 특정 값을 제외한 데이터 찾기

```
SELECT 특성_1, 특성_2
FROM 테이블_이름
WHERE 특성_2 <> "특정 값";

-- 예시
SELECT customers.FirstName, customers.LastName
FROM customers
WHERE customers.FirstName <> 'Helena';
```

- 특정 값보다 크거나 작은 데이터를 필터할 때에는 '<', '>', 비교하는 값을 포함하는 '이상', '이하' 값은 '<=', '>=' 을 사용합니다.

```
SELECT 특성_1, 특성_2
FROM 테이블_이름
WHERE 특성_1 > "특정 값";

SELECT 특성_1, 특성_2
FROM 테이블_이름
WHERE 특성_1 <= "특정 값";
```

- 문자열에서 특정 값과 비슷한 값들을 필터할 때에는 `LIKE` 와 '%' 혹은 '*' 를 사용합니다.

```
SELECT 특성_1, 특성_2
FROM 테이블_이름
WHERE 특성_2 LIKE "%특정 문자열%";

-- 예시
SELECT customers.FirstName, customers.LastName
FROM customers
WHERE customers.LastName LIKE '%han%';
```

- 리스트의 값들과 일치하는 데이터를 필터할 때에는 `IN` 을 사용합니다.

```
SELECT 특성_1, 특성_2
FROM 테이블_이름
WHERE 특성_2 IN ("특정값_1", "특정값_2");

-- 예시
SELECT customers.FirstName, customers.LastName
FROM customers
WHERE customers.LastName IN ('Hansen', 'Johansson');
```

- 값이 없는 `NULL` 과 같은 경우를 찾을 때에는 `IS` 를 사용합니다.

```
SELECT *
FROM 테이블_이름
WHERE 특성_1 IS NULL;

-- 예시
SELECT *
FROM employees
WHERE employees.ReportsTo is NULL;
```

> NULL 은 어떤 경우에 사용이 되나요?

- 값이 없는 경우를 제외할 때에는 `NOT` 을 추가해 이용합니다.

```
SELECT *
FROM 테이블_이름
WHERE 특성_1 IS NOT NULL;
```

### ***ORDER BY***

돌려받는 데이터 결과를 어떻게 정렬하지에 대한 선택적 항목입니다.

기본 정렬은 오름차순입니다.

```
SELECT *
FROM 테이블_이름
ORDER BY 특성_1;

-- 예시
SELECT *
FROM employees
ORDER BY employees.EmployeeId;
```

내림차순으로도 정렬할 수 있습니다.

```
SELECT *
FROM 테이블_이름
ORDER BY 특성_1 DESC;

-- 예시
SELECT *
FROM employees
ORDER BY employees.EmployeeId DESC;
```

### ***LIMIT***

돌려받는 데이터 결과 갯수를 정할 수 있습니다. 쿼리문에서 사용을 할 때에는 마지막에 추가해야 합니다.

데이터 결과 갯수를 200개 한정으로 정할 수 있습니다.

```
SELECT *
FROM 테이블_이름
LIMIT 200;

-- 예시
SELECT *
FROM customers
LIMIT 10;
```

### ***DISTINCT***

유니크한 값들을 받고 싶을 때에는 `SELECT` 뒤에 붙여 사용할 수 있습니다.

특성_1 기준으로 유니크한 값들만 선택합니다.

```
SELECT DISTINCT 특성_1
FROM 테이블_이름;

-- 예시
SELECT DISTINCT employees.Title
FROM employees;
```

특성_1, 특성_2, 특성_3 의 유니크한 '조합' 값들을 선택합니다.

```
SELECT
  DISTINCT
    특성_1
    ,특성_2
    ,특성_3
FROM 테이블_이름;
```

### ***INNER JOIN***

서로 공통된 부분으로만 연결합니다.

`INNER JOIN` 이나 `JOIN` 으로 실행할 수 있습니다.

```
SELECT *
FROM 테이블_1
JOIN 테이블_2 ON 테이블_1.특성_A = 테이블_2.특성_B;

-- 예시
SELECT
  c.FirstName || ' ' || c.LastName AS 'Customer Name',
  e.Firstname || ' ' || e.LastName AS 'Employee Name'
FROM customers AS c
JOIN employees AS e ON c.SupportRepId = e.EmployeeId;
```

### ***OUTER JOIN***

Outer JOIN 은 다양한 선택지가 있습니다.

저희는 기본적인 'left inclusive', 'right inclusive', 'full outer join' 을 볼 것입니다.

LEFT INCLUSIVE 은 `LEFT OUTER JOIN` 으로 진행을 할 수 있습니다.

```
SELECT *
FROM 테이블_1
LEFT OUTER JOIN 테이블_2 ON 테이블_1.특성_A = 테이블_2.특성_B
```

RIGHT INCLUSIVE 도 비슷하게 `RIGHT OUTER JOIN` 으로 진행할 수 있습니다.

```
SELECT *
FROM 테이블_1
RIGHT OUTER JOIN 테이블_2 ON 테이블_1.특성_A = 테이블_2.특성_B
```

> sqlite 에서는 RIGHT OUTER JOIN / FULL OUTER JOIN 을 지원하지 않기 때문에 순서를 바꾸어 LEFT JOIN 을 이용하는 방법을 사용합니다.

### ***여러 개 써보기***

이제 각 명령어를 통합해서 실행해보는 연습을 하겠습니다.

다음은 'Brazil' 에서 온 고객들을 도시별로 묶은 뒤에 각 도시 수에 따라 내름차순 정렬 후 CustomerId 에 따라 오름차순으로 정렬한 결과의 3개만 받는 예시입니다.

> 하지만 꼭 하나의 명령어만 정답이 아닐수도 있습니다. 아래 SQL 쿼리문을 어떻게 다르게 쓸 수 있을까요?

```
SELECT c.CustomerId, c.FirstName, count(c.City) as 'City Count'
FROM customers AS c
JOIN employees AS e ON c.SupportRepId = e.EmployeeId
WHERE c.Country = 'Brazil'
GROUP BY c.City
ORDER BY 3 DESC, c.CustomerId ASC
LIMIT 3;
```
