```ebnf+diagram
coercibility::= COERCIBILITY "(" expr ")"
```
The COERCIBILITY function is used to obtain the character set collation priority of the expr during the operation.

When expressions are evaluated, the default sorting rule is based on the character set collation. However, when two variables with different collations are operated on, only one collation can be selected as the sorting rule. The result of selecting the sorting rule typically depends on the collation priority. If two variables with different collations but the same priority are operated, the one that contains _bin will be chosen as the sorting rule; if neither contains _bin, an error will occur.

The priority of collations is shown in the table below. The smaller the Coercibility value, the higher the priority.

|Expression Type |Coercibility Value |Description |
|----------|---------------|-------------------|
| Explicitly Specified Collation | 0                   | Explicitly COLLATE specified.       |
| Calculated Collation  | 1                   | Concatenation of strings with different collations. |
| Implicitly Specified Collation | 2                   | Column values or local variables.    |
| System Variables       | 3                   | Variables used internally by the system. |
| Degradable Values      | 4                   | Literal strings.                     |
| Numeric Values         | 5                   | Numeric or temporal values.          |
| Ignorable Values       | 6                   | NULL or expressions that result in NULL. |

***Example*** for Standalone Deployment Heap tables

```sql
-- In the following statement, the system will use the explicitly specified utf8mb4_general_ci as the sorting rule for the operation
SELECT COERCIBILITY('aaa' collate utf8mb4_general_ci) res FROM dual;

               res
------------------
                 0
```
