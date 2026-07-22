YashanDB provides the following logical operators:

|Operator |Operands |Meaning |NULL Participation |
| --- | --- | --- | --- |
| AND | Binary  | A binary operator that returns true if both conditions are true; otherwise, it returns false. | Result is NULL |
| OR  | Binary  | A binary operator that returns true if at least one of the two conditions is true; otherwise, it returns false. | \*   true OR NULL: Result is true<br>\*   false OR NULL: Result is NULL<br>\*   NULL OR NULL: Result is NULL |
| NOT  | Unary  | A unary operator that returns false if the original condition is true; conversely, it returns true if the original condition is false. | Result is NULL |

The order of operations from highest to lowest priority is: NOT > AND > OR. You can use double parentheses () to adjust the desired order of operations.

***Example***

```sql
-- Create logics table and insert data
CREATE TABLE logics (c1 INT, c2 CHAR(1), c3 INT);
INSERT INTO logics VALUES (1,'a',3);
COMMIT;
   
-- Logical calculations in WHERE clause
SELECT c1,c2,c3 FROM logics WHERE 1=1 AND 1=0;
          C1 C2              C3
------------ ----- ------------

SELECT c1,c2,c3 FROM logics WHERE 1=1 OR 1=0;
          C1 C2              C3
------------ ----- ------------
           1 a                3
  
SELECT c1,c2,c3 FROM logics WHERE NOT (1<>1);
          C1 C2              C3
------------ ----- ------------
           1 a                3
   
SELECT SYSDATE FROM dual WHERE NOT 1=2 and 1=2;
SYSDATE
--------------------------------
```