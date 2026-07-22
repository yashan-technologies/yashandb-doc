```ebnf
find_in_set = FIND_IN_SET "(" expr "," strlist ")".
```

FIND_IN_SET函数用于查找[expr](../通用SQL语法/expr)表示的字符串在字符串列表strlist中首次出现的位置，函数返回值为INT类型。

本函数遵循如下规则：

- 在行式计算中，expr和strlist的值不能为RAW类型。在向量化计算中，expr和strlist的值不能为时区类型和RAW类型。
- 对大小写不敏感。

- 位置从1开始计数，如果strlist中不存在expr则返回0。

- strlist字符串列表是由','分割的子串组成的字符串。基于此规则，当expr中包含','时，函数将不能保证返回结果的正确性。

**expr, strlist**

- 在行式计算中，expr和strlist的值不能为RAW类型。

- 在向量化计算中，expr和strlist的值不能为布尔型和RAW类型。

- 在向量化执行引擎中，expr不能为LOB类型的行外存储数据。

- expr不能为超过65534字节的BFILE、LOB类型数据。

- 当expr或strlist中任一值为NULL时，函数返回NULL。

示例

```sql
SELECT FIND_IN_SET('A', 'a,b,c') a,
FIND_IN_SET('Shenzhen', 'Guangdong provice,Shenzhen city') b,
FIND_IN_SET(3, '1,2,3') c,
FIND_IN_SET('Shenzhen city', 'Guangdong,Shenzhen') d
FROM DUAL;

           A            B            C            D
------------ ------------ ------------ ------------
           1            0            3            0
```
