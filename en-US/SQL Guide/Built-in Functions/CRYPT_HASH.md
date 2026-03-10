```ebnf+diagram
crypt_hash::= CRYPT_HASH"(" expr1 "," expr2 ")" 
```

The CRYPT_HASH function computes a hash for the data in [expr1](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) using the hash algorithm type specified in [expr2](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), generating the corresponding digest and returning a hash digest in HEX format as a VARCHAR type.

> **Note**:
>
> If you want to use this function and the OpenSSL tool, please first refer to [Dependency Preparation](../../All Manuals/Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Preparing Dependencies) to check and ensure that the required tools are installed on the server system.

This function follows these rules:

- expr1/expr2 supports only VARCHAR/CHAR types.

- expr2 supports only the SM3 national cryptography algorithm.

- It returns null when expr1 is null.

***Example***

```sql
CREATE TABLE yashandb_hash_test(data  VARCHAR(255));
INSERT INTO yashandb_hash_test VALUES('udfghuiguighjijhihihjihjsihdjiasgdigsiajgijdsaji');
INSERT INTO yashandb_hash_test VALUES('fkdsjfklasdjkfjakohdfkohasoihjdfioasdhfioh');

SELECT CRYPT_HASH(data, 'sm3') hash_value FROM yashandb_hash_test;

HASH_VALUE
---------------------------------------------------------------- 
50AD45893C2AC2413926ECEECC654C1FFE3D2BC196EEC3A19526D8388F490F30 
B9F885B217D710B3D5524B7938FC09BB269C47AB52387A7180AE13E1EC5BAD26 


SELECT CRYPT_HASH('hjdhsafdhasdasfdfjdn', 'sm3') hash_value FROM dual;

HASH_VALUE                                                       
---------------------------------------------------------------- 
DF47341FF21A5E9E395865CB26291684AB01D5CD07B38BBC0D9195B292372519 

```
