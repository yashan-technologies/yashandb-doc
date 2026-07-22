```ebnf
coalesce = COALESCE "(" expr "," expr  {"," expr} ")".
```

The COALESCE function finds the first non-null expr value among multiple [expr](../General SQL Syntax/expr) values and returns it. If none is found, it returns NULL. The number range of expr is [2,100].

The data types of expr values can be any of the types supported by YashanDB, but all expr values in the same parameter list must belong to the same category below; otherwise, a function return type not supported error will occur:

*   Numeric: prioritized from high to low as DOUBLE, FLOAT, NUMBER, BIGINT, INT, SMALLINT, TINYINT, but the BIT type can only be in the same parameter list with the same type.
*   Character:
    *   prioritized from high to low as VARCHAR, CHAR.
    *   prioritized from high to low as NVARCHAR, NCHAR.
*   DateTime: prioritized from high to low as TIMESTAMP, DATE, TIME, but INTERVAL YEAR TO MONTH, INTERVAL DAY TO SECOND can only be in the same parameter list with the same type.
*   Boolean: BOOLEAN
* CLOB
* BLOB
* NCLOB
* BFILE

If all expr values have data types within the same category (character data includes two subcategories) but with different types, the function will perform type conversion according to the priority order listed above. For example, in `(expr1 NUMBER, expr2 SMALLINT, expr3 FLOAT)`, the data types of expr1 and expr2 will be converted to FLOAT, and the function will return a FLOAT type value.

When all expr values in the same parameter list are NULL, the function returns NULL.

***Example***

```sql
-- The numbers_nobit table contains the following fields and data
SELECT numbera,numberb,numberc,numberd,numbere,numberf,numberg FROM numbers_nobit;
     NUMBERA NUMBERB  NUMBERC               NUMBERD     NUMBERE     NUMBERF     NUMBERG
------------ ------- -------- --------------------- ----------- ----------- -----------
          -5      55     5555   5555555555555555555  5.555E+000  5.556E+000         555
 
-- Convert according to the highest data type priority among three exprs, numbera is converted to FLOAT type
SELECT COALESCE(numbera,numberb,numbere) res FROM numbers_nobit;
        RES 
----------- 
  -5.0E+000

 
SELECT COALESCE('','',true) res FROM DUAL;
RES                  
-------------------- 
true    
```