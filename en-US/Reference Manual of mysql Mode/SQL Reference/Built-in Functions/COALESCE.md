```ebnf
coalesce = COALESCE "(" expr "," expr  {"," expr} ")".
```

The COALESCE function finds the first non-null expr value among multiple [expr](../General SQL Syntax/expr) values and returns it. If none is found, it returns NULL. The number range of expr is [1,65535].

The function compares input parameters pairwise in sequence, following the same comparison rules as [IFNULL](./IFNULL).

***Example*** for Heap tables

```sql
-- The numbers_nobit table contains the following fields and data
SELECT numbera,numberb,numberc,numberd,numbere,numberf,numberg FROM numbers_nobit;
     numbera numberb  numberc               numberd     numbere     numberf     numberg
------------ ------- -------- --------------------- ----------- ----------- -----------
          -5      55     5555   5555555555555555555  5.555E+000  5.556E+000         555
 
-- Convert according to the highest data type priority among three exprs, numbera is converted to FLOAT type
SELECT COALESCE(numbera,numberb,numbere) res FROM numbers_nobit;
        res 
----------- 
  -5.0E+000
```