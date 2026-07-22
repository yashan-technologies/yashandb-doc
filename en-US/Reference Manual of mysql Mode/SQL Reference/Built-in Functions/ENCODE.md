```ebnf
encode = ENCODE "(" str "," pass_str")".
```

The ENCODE function uses pass_str as a password to encrypt str. The return value is of VARBINARY type, with length the same as str length.

***Example*** for  Heap tables

```sql
SELECT ENCODE('abc', 'value') res FROM DUAL;
res       
--------- 
A77DA3   
```
