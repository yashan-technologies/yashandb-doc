```ebnf
regexp_like = REGEXP_LIKE "(" expr "," regexp ["," match_para] ")".
```

REGEXP_LIKE()为正则表达式函数，用于在源字符串[expr](../通用SQL语法/expr)按正则模式匹配目标字符串regexp并返回匹配结果，返回值为BIGINT类型，匹配成功时返回1，否则返回0。

[REGEXP/RLIKE表达式](../通用SQL语法/condition.md)是REGEXP_LIKE()函数的同义词，仅返回值数据类型不同，REGEXP和RLIKE条件表达式的返回数据类型为TINYINT。

**expr**

通用表达式，其值须为字符型或可转换为字符型的其他类型。

- expr不能为超过65534字节的TEXT类型数据。

- 函数使用输入字符集定义的字符计算字符串（仅支持UTF-8）。

- 当expr的值为NULL时，函数返回NULL。

**regexp**

指定一个目标字符串[RegExp](../通用SQL语法/RegExp)，长度不超过512字节。

- regexp不支持为posix字符。

- 当regexp为NULL时，函数返回NULL。

**match\_para**

正则匹配参数，可省略或指定为NULL，参数可选值如下：

- 'i'：大小写不敏感。

- 'c'：大小写敏感。

- 'n'：允许句点（.）匹配任何字符，包括换行符。默认情况下，句点与换行符不匹配。

- 'm'：将字符串视为多行，将^和$分别解释为字符串中任意行的开始和结束，而不只是整个字符串的开始或结束。默认情况下，将字符串视为一行。

- 'x'：忽略空白和#注释。默认情况下，空白字符与其自身匹配。

match\_para参数指定为可选值以外的其他值时，函数将返回YAS-07202错误。

当`match_para`省略时，判断`expr`和`regexp`是否指定了字符序`collation`，根据字符序的大小写敏感性进行正则匹配；没有指定`expr`和`regexp`的字符序`collation`时，使用系统默认字符序`utf8mb4_general_ci`，大小写不敏感。

示例（单机HEAP表）

```sql
-- 数值转字符时，会删除“.0”，然后再进行匹配

select regexp_like(1, 1.0), regexp_like(0, 0.0);

  regexp_like(1, 1.0)   regexp_like(0, 0.0)
--------------------- ---------------------
                    1                     1

select (1 rlike 1.0), (0 rlike 0.0);

1 rlike 1.0 (0 rlike 0.0)
----------- -------------
          1             1

select cast(1e5 as char(10)) res1, regexp_like(1e5, 100000) res2, 
cast(1.0e5 as char(10)) res3, regexp_like(1.0e5, 100000) res4;

res1                          res2 res3                           res4
------------ --------------------- ------------- ---------------------
1.0E+005                         0 1.0E+005                          0

select cast(1e5 as char(10)) res1, (1e5 rlike 100000) res2, 
cast(1.0e5 as char(10)) res3, (1.0e5 rlike 100000) res4;

res1            res2 res3                 res4
-------------- ----- ------------------- -----
1.0E+005           0 1.0E+005                0


SELECT REGEXP_LIKE('aa\naa','^aa$','i') reg1, 
REGEXP_LIKE('aa','^aa$','i') reg2 FROM DUAL;

                 reg1                  reg2
--------------------- ---------------------
                    0                     1

SELECT ('aa\naa' rlike '^aa$') reg1, ('aa' rlike '^aa$') reg2 FROM DUAL;

 reg1  reg2
----- -----
    0     1


SELECT REGEXP_LIKE('AA','A.A') reg1, 
REGEXP_LIKE('AA','A.A','n') reg2 FROM DUAL;

                 reg1                  reg2
--------------------- ---------------------
                    0                     0

SELECT ('AA' rlike 'A.A') reg1, ('AA' rlike 'A.A') reg2 FROM DUAL;

 reg1  reg2
----- -----
    0     0

SELECT REGEXP_LIKE('-654196584','^-[0-9]*[1-9][0-9]*$') reg1, 
REGEXP_LIKE('654196584','^[0-9]*[1-9][0-9]*$','m') reg2 FROM DUAL;

                 reg1                  reg2
--------------------- ---------------------
                    1                     1

SELECT ('-654196584' rlike '^-[0-9]*[1-9][0-9]*$') reg1, 
('654196584' rlike '^[0-9]*[1-9][0-9]*$') reg2 FROM DUAL;

 reg1  reg2
----- -----
    1     1

```
