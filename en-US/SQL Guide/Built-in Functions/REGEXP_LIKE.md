```ebnf+diagram
regexp_like::= REGEXP_LIKE "(" expr "," regexp ["," match_para] ")"
```

REGEXP_LIKE is a regular expression function that matches the target string `regexp` against the source string [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) according to the regular pattern and returns the match result. The return value is of Boolean type, returning TRUE when the match is successful, otherwise returning FALSE.

This function is similar to the LIKE syntax, but unlike LIKE which can only use '%' and '_' wildcards, this function's target string `regexp` can use regular expressions.

**expr**

A general expression whose value must be of character type or another type that can be converted to character type.

- `expr` cannot be BFILE or LOB type data exceeding 65534 bytes.

- The function uses character calculations defined by the input character set (only supports UTF-8).

- When the value of `expr` is NULL, the function returns NULL.

**regexp**

Specifies a [RegExp](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/RegExp) with a length not exceeding 512 bytes.

When `regexp` is NULL, the function returns NULL.

**match_para**

Regular matching parameters, with the same meaning as in [REGEXP_COUNT](REGEXP_COUNT.html#match_para).

***Example***

```sql
SELECT REGEXP_LIKE('aa\naa','^aa$','i') reg1,
REGEXP_LIKE('aa','^aa$','i') reg2 FROM DUAL;
REG1                 REG2              
-------------------- --------------------
false                true             
 
SELECT REGEXP_LIKE('AA','A.A') reg1,
REGEXP_LIKE('AA','A.A','n') reg2 FROM DUAL;
REG1                 REG2                
-------------------- --------------------
false                false     
 
SELECT REGEXP_LIKE('-654196584','^-[0-9]*[1-9][0-9]*$') reg1,
REGEXP_LIKE('654196584','^[0-9]*[1-9][0-9]*$','m') reg2 FROM DUAL;        
REG1                 REG2                
-------------------- --------------------
true                 true
```
