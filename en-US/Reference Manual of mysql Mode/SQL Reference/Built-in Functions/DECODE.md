```ebnf
decode = DECODE "(" crypt_str "," pass_str ")".
```

The DECODE function uses pass_str as the password to decrypt the encrypted string crypt_str. The return value is of VARBINARY type, with the same length as crypt_str.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT DECODE(ENCODE('abc', 'value'), 'value') res FROM DUAL;
res                                                              
---------------------------------------------------------------- 
616263  
```
