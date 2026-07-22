The value of Boolean type data can only be 1 (TRUE) and 0 (FALSE), with a byte length of 1, and can be used to indicate the status of a binary feature (for example, whether the user is VIP). Additionally, this type is also the output type for many conditions (such as comparison operations, LIKE/NOT LIKE statements, etc.), and is widely used in WHERE and HAVING clauses to filter data.

YashanDB's rules for handling Boolean type data:

- Allows the following values to be inserted into Boolean fields (case insensitive).

    |Type |Input Value |Conversion Value |
    | --- | --- | --- |
    | Character  | 'true', 't', 'yes', 'y', 'on', '1' | 1                |
    | Character  | 'false', 'f', 'no', 'n', 'off', '0' | 0                |
    | Identifier | true                            | 1                |
    | Identifier | false                           | 0                |
    | Integer    | Non-zero integer                | 1                |
    | Integer    | 0                               | 0                |

- Allows conversion between Boolean type and integer values, where non-zero integers can convert to true, but true can only convert to integer 1.

- Allows comparison between Boolean type and integer values.

***Example***

```sql
CREATE TABLE bools(c_b1 BOOLEAN, c_b2 BOOLEAN, c_b3 BOOLEAN, c_b4 BOOLEAN);
  
-- Insert other types converted to Boolean
INSERT INTO bools VALUES('t', 'no', 'on', 4);
SELECT c_b1,c_b2,c_b3,c_b4 FROM bools;
C_B1                 C_B2                 C_B3                 C_B4               
-------------------- -------------------- -------------------- --------------------
true                 false                true                 true
  
-- Boolean to integer for comparison
SELECT CASE CAST(c_b1 as INT)
WHEN 1 THEN '11111'
WHEN 2 THEN '22222'
END b
FROM bools
WHERE c_b2<4;
B       
---------
11111
```
