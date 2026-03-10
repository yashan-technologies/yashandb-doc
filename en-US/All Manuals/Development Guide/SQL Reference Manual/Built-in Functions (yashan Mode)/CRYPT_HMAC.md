```ebnf+diagram
crypt_hmac::= CRYPT_HMAC"(" expr1 "," expr2 "," expr3 ")" 
```

The CRYPT_HMAC function computes the HMAC of [expr1](../General SQL Syntax/expr) using [expr3](../General SQL Syntax/expr) as the key and [expr2](../General SQL Syntax/expr) as the algorithm type, returning a HMAC digest in HEX format as a VARCHAR type.

> **Note**:
>
> If you need to use this function and the OpenSSL tool, please first refer to [Dependency Preparation](../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Preparing Dependencies) to check and ensure that the required tools are installed on the server system.

This function follows these rules:

- expr1/expr2/expr3 only support VARCHAR/CHAR types.

- expr2 only supports the SM3 national cryptography algorithm.

- When expr1 is null, it returns null.

- expr2 and expr3 cannot be null; if they are null, the function will throw an error.

***Example***

```sql
CREATE TABLE yashandb_hmac_test(data  VARCHAR(255));
INSERT INTO yashandb_hmac_test VALUES('udfghuiguighjijhihihjihjsihdjiasgdigsiajgijdsaji');
INSERT INTO yashandb_hmac_test VALUES('fkdsjfklasdjkfjakohdfkohasoihjdfioasdhfioh');

SELECT CRYPT_HMAC(data, 'sm3', 'yashandb') hmac_value FROM yashandb_hmac_test;

SELECT CRYPT_HMAC(data, 'sm3', 'yashandb') hmac_value FROM yashandb_hmac_test;

HMAC_VALUE
---------------------------------------------------------------- 
22DEEF018DF55D9EA8E74D95D5A2C83E90927737026663B1299DA9CAE995F2A1 
EB34CE6C5C2623674D7E6ECFA95F2A8892AB97F38414A0B52F3382F259E48761 


SELECT CRYPT_HMAC('hjdhsafdhasdasfdfjdn', 'sm3', 'yashandb') hmac_value FROM dual;

HMAC_VALUE                                                       
---------------------------------------------------------------- 
AA1DF9C2BA35F1C20CB3AB82CE144BD4B2FFC0894CEA5B054920514D76DC833B 

```
