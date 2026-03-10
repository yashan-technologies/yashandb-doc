```ebnf+diagram
interval::= INTERVAL "(" expr ", " expr0 ", " expr1 ", " expr2 ", ..." ")"
```

比较函数INTERVAL用于查找第一个参数在后续的n个参数列表中的排序位置，该函数会使用第一个expr按顺序与后续n个expr进行比较判断，当找到第一个大于expr的值时，返回对应的位置索引。例如当expr0大于expr时返回0，expr1大于expr时返回1，如果列表中没有大于第一个expr的值则返回n+1。


- 此函数入参个数要求为2-65535之间。

- 此函数在进行数值比较时，会将入参的数据类型按照下表进行转换后再进行比较。

    |入参数据类型|转换后数据类型|
    |-----------|-------------|
    |BOOLEAN/TINYINT/TINYINT UNSIGNED/<br>SMALLINT/SMALLINT UNSIGNED/INT/DATE|INTEGER|
    |BIGINT/TIMESTAMP（精度为0）|BIGINT|
    |BIGINT UNSIGNED/INT UNSIGNED|BIGINT UNSIGNED|
    |NUMBER/TIMESTAMP（精度不为0）|NUMBER|
    |FLOAT/DOUBLE/CHAR/VARCHAR|DOUBLE|
    |TEXT/BLOB|NUMBER|

- 当第一个expr为null时返回-1，后续n个expr中有null时选择下一个expr进行比较。

- 暂不支持入参为非纯数值的字符类型和BINARY类型。

示例（单机HEAP表）

```sql
SELECT INTERVAL(10, 5.2, 9.8, 10.1, 15, 20);

INTERVAL(10, 5.2, 9.8, 10.1, 15, 20)
------------------------------------
                                   2


SELECT INTERVAL(TRUE, FALSE, TRUE, FALSE);

INTERVAL(TRUE, FALSE, TRUE, FALSE)
----------------------------------
                                 3


SELECT INTERVAL('10', '5', '10', '15', '20');

INTERVAL('10', '5', '10', '15', '20')
-------------------------------------
                                    2


SELECT INTERVAL(NULL, 5, 10, 15);

INTERVAL(NULL, 5, 10, 15)
-------------------------
                       -1


SELECT INTERVAL(10, 5, NULL, NULL, NULL, NULL, 15, 20);

INTERVAL(10, 5, NULL, NULL, NULL, NULL, 15, 20)
-----------------------------------------------
                                              5


SELECT INTERVAL('10abc', '5.2', '9.8' '10.1', '15', '20');

[1:39]YAS-04209 unexpected word '10.1'

SELECT INTERVAL(1);

[1:8]YAS-04309 arguments count must be between 2 and 65535
```
