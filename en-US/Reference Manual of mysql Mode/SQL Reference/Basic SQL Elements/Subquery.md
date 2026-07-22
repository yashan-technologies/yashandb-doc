
A subquery refers to a query that is nested inside another SQL statement (e.g., SELECT, FROM, WHERE, HAVING). It can return a single row, single column, multiple rows, or multiple columns of data, used for dynamically generating filters, computed fields, or temporary datasets.

Generally, subqueries can be categorized into non-correlated subqueries (also known as "static subqueries"), correlated subqueries, and scalar subqueries.

|Type |Dependent on External Query |Return Result |Typical Scenarios |
|----------------|--------------|-------------------|--------------------------|
| Non-correlated Subquery | No                   | Single Row/Multiple Rows/Multiple Columns | IN, EXISTS, Comparison Operations |
| Correlated Subquery | Yes                       | Single Row/Multiple Rows/Multiple Columns | EXISTS, NOT EXISTS                  |
| Scalar Subquery    | Either                     | Single Row Single Column | SELECT List, WHERE                  |

## Non-correlated Subquery

### Definition

Independent of the external query, it **does not depend on the columns or values of the external query**, executed only once, and then the result is passed to the external query.

### Characteristics

- **Execution Order**: Execute the subquery first, then the external query.
- Commonly found in scenarios such as IN, EXISTS, comparison operators, etc.

### SQL Example

```sql
-- Query employees whose entry date is after the median entry date
SELECT employee_no, employee_name, entry_date
FROM employees
WHERE entry_date > (
  SELECT median(entry_date)
  FROM employees
);

EMPLOYEE_NO   EMPLOYEE_NAME ENTRY_DATE                                       
------------- ------------- ------------------------------------------------ 
0201010011    Anna          2024-05-11                                      
0201008004    Jim           2024-08-19                                      
```

## Correlated Subquery

### Definition

The subquery references columns from the external query and requires **dependence on the results of the external query**, executing the subquery once for each row processed by the external query.

### Characteristics

- **Execution Order**: The external query executes row by row, while the subquery matches in a loop.
- Commonly found in scenarios such as EXISTS, NOT EXISTS, etc.

### SQL Example

```sql
SELECT employee_no, employee_name
FROM employees e1
WHERE EXISTS (
  SELECT 1
  FROM employees e2
  WHERE e2.employee_no = e1.employee_no
);

EMPLOYEE_NO   EMPLOYEE_NAME 
------------- ------------- 
0101000001    Mask         
0101000002    John         
0201008003    Jack         
0201008004    Jim          
0201010011    Anna      
```

## Scalar Subquery

### Definition

A subquery that returns a **single row single column** (scalar value) can be used like a constant in SQL.

### Characteristics

- It is essential to ensure that it returns a unique row.
- Commonly found in positions such as SELECT list, WHERE conditions, etc.

### SQL Example

```sql
-- Display employee entry dates and their department's median entry date
SELECT 
  employee_no,
  employee_name,
  entry_date,
  (SELECT median(entry_date) FROM employees e2 
   WHERE e2.department = e1.department) AS dept_avg_entry_date
FROM employees e1;

EMPLOYEE_NO   EMPLOYEE_NAME ENTRY_DATE                                       DEPT_AVG_ENTRY_DATE                              
------------- ------------- ------------------------------------------------ ------------------------------------------------ 
0101000001    Mask          2022-06-11                                       2021-01-27                                      
0101000002    John          2019-09-15                                       2021-01-27                                      
0201010011    Anna          2024-05-11                                       2024-05-11                                      
0201008003    Jack          2023-04-07                                       2023-12-13                                      
0201008004    Jim           2024-08-19                                       2023-12-13                  
```
