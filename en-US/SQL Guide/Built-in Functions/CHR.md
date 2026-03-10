```ebnf+diagram
chr::= CHR "(" expr ")"
```

The CHR function converts the decimal code represented by [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) into the corresponding character according to the database character set, returning a result of VARCHAR type.

**expr**

A general expression, the value of which must be a numeric type other than BIT or a character type that can be converted to NUMBER type.

*   If the value of expr is a decimal, the function will first apply [FLOOR](FLOOR) on it, and the resulting integer must be within the range [0, 2<sup>32</sup>-1]; otherwise, an Out of range error is returned.
*   If the value of expr is NULL, the function returns NULL.
*   Depending on the database character set, the handling of parameters differs:
    *   For single-byte character sets, the code value range is [0,255]. If the value of expr is less than 0, the function returns an error. If the value of expr is greater than 255, the function applies 256 [MOD](MOD) (i.e., `MOD(expr,256)`) before conversion.
    *   For multi-byte character sets, n must parse as a complete encoding; invalid encodings are not validated. In the LSC table or TAC table, the actual performance of multi-byte character sets is equivalent to that of single-byte character sets.

***Example***

```sql
-- Assuming expr is character type data '2344556.44' and the database server character set is ASCII
-- 1. The function will first convert it to NUMBER type, resulting in the decimal 2344556.44
-- 2. It will apply FLOOR(2344556.44), yielding the integer 2344556
-- 3. Since the value is greater than 255, it will apply MOD(2344556,256), resulting in 108
-- 4. Finally, it will execute the ASCII code value conversion
SELECT CHR('2344556.44') res FROM DUAL;
RES   
----- 
l   

-- Assuming the database server character set is UTF8
SELECT CHR(14844052) RES FROM DUAL;
RES
-----
——
```
