```ebnf
ora_hash = ORA_HASH "(" expr [ "," max_bucket [  "," seed_value]  ] ")".
```

ORA_HASH is a function that computes the hash value of a given expression [expr](../General SQL Syntax/expr) and returns a result of type NUMBER.

This function adheres to the following rules:

- This function is supported only for HEAP tables.

- The expr in this function is usually parsed as a column name and is a required parameter.

- The result of this function is of type NUMBER; if any parameter is NULL, the result will be NULL.

**expr**

A general expression, its value can be:

- An input string or any non-LOB type data column.

- NULL, in which case the function returns NULL.

**max_bucket**

The maximum bucket value returned by the hash function, which is optional, can be:

- Any value between 0 and 4294967295, with a default value of 4294967295.

- NULL, in which case the function returns NULL.

- If the given value has a decimal, the decimal will be truncated, leaving only the integer part.

**seed_value**

The seed value used to generate different hash results, which is optional and can be combined with max_bucket:

- Any value between 0 and 4294967295, with a default value of 0.

- NULL, in which case the function returns NULL.

- If the given value has a decimal, the decimal will be truncated, leaving only the integer part.

***Example*** for Heap tables

```sql

SELECT
    ORA_HASH(NULL) NULL_1,
    ORA_HASH(NULL, NULL) NULL_2,
    ORA_HASH(NULL, NULL, NULL) NULL_3
FROM dual;

NULL_1        NULL_2        NULL_3
------        ------        ------

SELECT ORA_HASH('Hello!') AS hash_value FROM dual;

HASH_VALUE
----------
656566124            
            
SELECT 
  ORA_HASH('Hello', 1000) AS hash_seed,
  ORA_HASH('Hello', 1000, 0) AS hash_seed_0,
  ORA_HASH('Hello', 1000, 1) AS hash_seed_1
FROM dual;

HASH_SEED HASH_SEED_0 HASH_SEED_1
--------- ----------- -----------
66                 66         335
 
SELECT ORA_HASH('Hello', 0, 1) AS hash_value FROM dual;

HASH_VALUE
----------
0 
```
