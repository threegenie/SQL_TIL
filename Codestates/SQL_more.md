## **SQL 내장함수**

SQL 에서는 자체적으로 사용할 수 있는 함수들이 여러 개 있습니다. 값들을 더하거나 평균을 구하거나 하는 등의 다양한 작업들을 손쉽게 할 수 있도록 도와줍니다.

### **집합연산**

레코드들을 조회하고 분류한 뒤에 특정 작업을 하는 등의 집합연산에 대해서 알아보겠습니다.

### **GROUP BY**

데이터를 조회하게 될 때 묶어서 조회하게 해주는 기능을 먼저 살펴보겠습니다. SQL 에서는 GROUP BY 를 사용해 조회한 데이터를 분류해서 묶을 수 있습니다.

예를 들어 다음과 같은 쿼리가 있다고 해보겠습니다.

```
SELECT * FROM customers;
```

위 쿼리에서는 단순히 customers 테이블로부터 데이터를 조회하고 있습니다. 이번에는 각 주 (state) 를 기반으로 그룹화 해보겠습니다.

```
SELECT * FROM customers
GROUP BY State;
```

위 쿼리처럼 간단하게 State 를 기준으로 그룹화할 수 있습니다. 하지만 돌아온 결과를 보게되면 데이터가 중간에 비어있는 모습들을 확인하실 수 있습니다.

데이터베이스에서는 그룹으로 따로 묶었지만 이에 대한 작업을 따로 하지 않고 그저 조회만 했기 때문에 이에 대한 결과는 각 그룹의 첫번째 데이터가 보이게 되는 겁니다.

따라서 다음과 같이 정렬을 하게 되면 각 그룹의 첫번째 레코드가 조회되는 것을 확인하실 수 있습니다.

```
SELECT * FROM customers
GROUP BY State
ORDER BY CustomerId;
```

### **HAVING**

HAVING 은 GROUP BY 로 조회된 결과에 대한 필터입니다. 즉, 다음과 같은 쿼리가 있다고 생각하겠습니다.

```
SELECT CustomerId, AVG(Total)
FROM invoices
GROUP BY CustomerId
HAVING AVG(Total) > 6.00
```

위 쿼리에서는 각 고객의 주문한 값들의 평균을 구한 뒤에 그 평균이 6.00 을 넘는 결과들만 조회하고 있습니다.

이처럼 GROUP BY 로 인한 결과에 대한 필터를 적용할 때에는 HAVING 을 사용할 수 있습니다.

이에 비해 WHERE 는 적용하는 방식이 다릅니다. HAVING 은 그룹화한 결과에 대한 필터라고 생각한다면 WHERE 는 그룹화하기 전에 조회되는 레코드를 필터합니다. 따라서 실제로 GROUP BY 전에 데이터를 필터하고 싶다면 WHERE, GROUP BY 결과에 대한 필터는 HAVING 을 사용하시면 됩니다.

그룹화를 했으니 이제 묶여진 그룹에 대해서 어떤 작업들을 할 수 있는지 알아보겠습니다.

### **COUNT()**

집계할 때 유용한 COUNT 함수는 말 그대로 몇 개인지 값을 리턴합니다. 사용법은 다음과 같습니다.

```
SELECT *, COUNT(*) FROM customers
GROUP BY State;
```

실제로 실행하게 되면 각 그룹의 첫번째 레코드와 각 그룹에 대한 집계를 리턴합니다. 조금 다듬게 된다면 다음과 같이 필요한 정보만 조회할 수 있습니다.

```
SELECT State, COUNT(*) FROM customers
GROUP BY State;
```

이런 식으로 조회할 칼럼들을 묶어둔 그룹을 기준으로 정해 한 눈에 볼 수 있습니다.

### **SUM()**

SUM 은 합을 구해주는 함수입니다. 조회된 값들에 대한 합을 구해 리턴합니다.

사용법은 다음과 같습니다.

```
SELECT InvoiceId, SUM(UnitPrice)
FROM invoice_items
GROUP BY InvoiceId;
```

위 코드는 'invoice_items' 라는 테이블에서 'InvoiceId' 필드를 기준으로 그룹화 후 'UnitPrice' 필드에 대한 값들에 대한 합을 구하고 있습니다.

이처럼 어떤 값들에 대한 합을 구할 때 매우 유용하게 사용할 수 있습니다.

### **AVG()**

AVG 는 평균값을 구해주는 함수입니다. 사용방법은 다음과 같습니다.

```
SELECT TrackId, AVG(UnitPrice)
FROM invoice_items
GROUP BY TrackId;
```

위 쿼리문은 각 TrackId 의 평균 'UnitPrice' 를 구하고 있습니다.

### **MAX(), MIN()**

MAX 와 MIN 은 최대값과 최소값을 구할 수 있는 함수입니다.

다음과 같이 사용할 수 있습니다.

```
SELECT CustomerId, MIN(Total)
FROM invoices
GROUP BY CustomerId
```

위 코드에서는 CustomerId 로 그룹화한 뒤에 각 고객의 최소값들을 보여주고 있는 쿼리입니다.

> MIN 을 MAX 로만 바꾸면 각 고객이 지불한 최대 금액을 명시할 수 있습니다.

### **SELECT 실행 순서**

SELECT 문은 데이터를 조회하는 쿼리문에 사용이 됩니다. 그런데 쿼리문이 적힌 순서가 아닌 정해진 순서대로 작동을 하게 됩니다.

실행 순서는 다음과 같습니다.

- FROM
- WHERE
- GROUP BY
- HAVING
- SELECT
- ORDER BY

한 번 쿼리로 보겠습니다.

```
SELECT CustomerId, AVG(Total)
FROM invoices
WHERE CustomerId >= 10
GROUP BY CustomerId
HAVING SUM(Total) >= 30
ORDER BY 2
```

위와 같은 쿼리문을 실행하게 되었을 때 동작하는 순서는 다음과 같습니다.

- `FROM invoices`: 먼저 invoices 테이블에 접근을 합니다.
- `WHERE CustomerId >= 10`: 'CustomerId' 필드가 10 이상인 레코드들을 조회합니다.
- `GROUP BY CustomerId`: 'CustomerId' 를 기준으로 그룹화합니다.
- `HAVING SUM(Total) >= 30`: 'Total' 필드의 값들의 합이 30 이상인 결과들만 필터합니다.
- `SELECT CustomerId, AVG(Total)`: 조회된 결과에서 'CustomerId' 필드와 'Total' 필드의 평균값을 가져옵니다.
- `ORDER BY 2`: `AVG(Total)` 필드를 기준으로 오름차순 정렬을 합니다.

이처럼 실제로 데이터베이스에서 돌아가는 쿼리의 순서가 있습니다.

### **CASE 사용하기**

SQL 에서도 프로그래밍 언어의 if 문과 같은 기능을 사용할 수 있습니다.

CASE 를 사용하게 되면 정해진 조건에 따라 다르게 결과를 받을 수 있습니다.

실제 SQL 예시를 보면 다음과 같습니다.

```
SELECT CASE
			WHEN CustomerId <= 25 THEN 'GROUP 1'
			WHEN CustomerId <= 50 THEN 'GROUP 2'
			ELSE 'GROUP 3'
		END
	FROM customers
```

위 쿼리문은 CustomerId 필드값에 따라 'GROUP 1', 'GROUP 2', 'GROUP 3' 으로 나누게 됩니다. 따라서 25 이하인 경우에는 'GROUP 1', 26부터 50 사이인 경우에는 'GROUP 2', 51 이상은 'GROUP 3' 으로 분류가 됩니다.

이렇게 SQL 에서 if 문을 활용할 수가 있습니다.

### **SUBQUERY**

쿼리문을 작성할 때 다른 쿼리문을 포함하는 것이 SUBQUERY (서브쿼리) 입니다. 즉, 실행되는 쿼리에 중첩으로 위치해 정보를 전달합니다. 서브쿼리는 소괄호로 감싸져 있습니다.

서브쿼리의 결과는 개별값들이나 레코드 리스트를 돌려줄 수 있습니다.

또한 서브쿼리의 결과를 하나의 칼럼으로 활용할 수 있습니다.

예를 들어 다음과 같습니다.

```
SELECT CustomerId, CustomerId = (SELECT CustomerId FROM customers WHERE CustomerId = 2)
FROM customers
WHERE CustomerId < 6
```

이제부터는 서브쿼리를 어떻게 사용할 수 있는지 더 자세히 알아보겠습니다.

### **IN, NOT IN**

IN 은 특정한 값들이 서브쿼리에 있는지 확인할 수 있습니다. 예를 들어 다음과 같은 쿼리를 살펴보겠습니다.

```
SELECT *
FROM customers
WHERE CustomerId IN (SELECT CustomerId FROM customers WHERE CustomerId < 10)
```

보시면 customers 테이블에서 'CustomerId' 의 값이 서브쿼리에서 돌려받는 값에 속한 결과들만 조회하고 있습니다. 서브쿼리에서는 'CustomerId' 가 10 이하인 데이터를 돌려주기 때문에 최종 조회된 데이터 또한 'CustomerId' 가 10 이하인 경우가 되겠습니다.

> 만약에 NOT IN 을 사용하게 된다면 서브쿼리에서 조회된 10 미만을 제외한 레코드를 조회하게 됩니다.

### **EXISTS**

EXISTS 혹은 NOT EXISTS 와 같은 경우에는 돌려받은 서브쿼리의 존재하는 레코드를 확인합니다.

만약에 조회된 레코드가 존재한다면 참을 아닌 경우에는 거짓을 리턴합니다. 따라서 이를 이용한 쿼리문을 다음과 같이 작성할 수 있습니다.

```
SELECT EmployeeId
FROM employees e
WHERE EXISTS (
	SELECT 1
	FROM customers c
	WHERE c.SupportRepId = e.EmployeeId
	)
ORDER BY EmployeeId
```

위 쿼리문에서는 employees 테이블에서부터 'EmployeeId' 필드를 조회합니다. 이 때 서브쿼리로 customers 테이블의 'SupportRepId' 필드값과 employees 테이블의 'EmployeeId' 필드값을 비교해 일치하는 레코드들을 가져옵니다.

### **FROM**

서브쿼리를 FROM 에도 사용할 수가 있습니다.

예를 들어 다음과 같이 사용할 수 있습니다.

```
SELECT *
FROM (
	SELECT CustomerId
	FROM customers
	WHERE CustomerId < 10
	)
```

위 쿼리문과 같이 서브쿼리를 사용해 조회된 결과를 하나의 테이블이나 조회할 대상으로 지정해 사용할 수가 있습니다.
