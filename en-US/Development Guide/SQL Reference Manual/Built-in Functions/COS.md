```ebnf
cos = COS "(" expr ")".
```

The COS function returns the cosine value of the given parameter, which is an angle expressed in radians. The value itself is unrestricted (limited only by the defined range of its data type), and the function will return a DOUBLE type value within the range of [-1, 1].

The value of [expr](../General SQL Syntax/expr) can be a numeric type or a character type that can be converted to NUMBER (a conversion failure results in an Invalid number error). For other types, the function returns a type not supported message.

When the value of expr is NULL, the function returns NULL.

***Example***

```sql
SELECT COS(30*3.1415926/180) res FROM DUAL;
        RES 
----------- 
  8.66E-001
    
SELECT COS(45*3.1415926/180) res FROM DUAL;
        RES 
----------- 
 7.071E-001
    
SELECT COS(60*3.1415926/180) res FROM DUAL;
        RES 
----------- 
   5.0E-001
```
