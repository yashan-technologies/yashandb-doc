```ebnf+diagram
ora_hash::= ORA_HASH "(" expr [ "," max_bucket [  "," seed_value]  ] ")"
```

ORA_HASH是一个计算给定表达式[expr](../通用SQL语法/expr)哈希值的函数，返回结果为一个NUMBER值。

本函数遵循如下规则：

- 本函数仅HEAP表支持。

- 本函数的expr通常解析为列名，为必须项目。

- 本函数的结果为NUMBER类型，任意参数为NULL则返回结果为NULL。

**expr**

通用表达式，其值可以为：

- 输入字符串或任意非LOB类型的数据列。

- NULL，此时函数返回NULL。

**max_bucket**

哈希函数返回的最大桶值，为可选项，其值可以为：

- 0~4294967295之间的任意值，默认值为4294967295。

- NULL，此时函数返回NULL。

- 只要给定值存在小数，直接将小数舍去，留下整数部分。

**seed_value**

种子值，用于生成不同的哈希结果，为可选项，可以和max_bucket进行组合：

- 0~4294967295之间的任意值，默认值为0。

- NULL，此时函数返回NULL。

- 只要给定值存在小数，直接将小数舍去，留下整数部分。

示例（HEAP表）

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
