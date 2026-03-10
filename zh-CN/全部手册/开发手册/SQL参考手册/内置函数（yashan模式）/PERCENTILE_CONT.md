```ebnf+diagram
percentile_cont::= PERCENTILE_CONT "(" percentile ")" WITHIN GROUP "(" ORDER BY expr [DESC|ASC] [NULLS FIRST|LAST] ")"  [OVER query_partition_clause]
```

PERCENTILE_CONT函数假定表达式[expr](../通用SQL语法/expr)连续分布，在组内对其进行排序操作，忽略数据为null的数据行，给定一个百分比值percentile，返回对应百分比所在的插值。当给定的percentile为0.5时，返回结果是组内排序键的中位数。

不指定OVER关键字语法时，本函数是聚合函数。

本函数不支持向量化计算。

**percentile**

百分比值percentile只允许数值类型和可隐式转换为数值类型的其他数据类型。

percentile取值范围为[0, 1]，否则报错。

percentile在每一个聚合组内必须是常量。

**expr**

指定函数组内排序键。

expr只允许数值类型、时间类型。

当expr的类型为非浮点数的数值类型时，函数返回number类型。expr为时间类型和浮点数类型时，函数返回与expr相同的类型。

**函数线性插值算法**

对排序键进行排序，得到有序数据。给定的百分比值percentile记为P，组内非空数据行数为N。通过公式RowNum = 1 + P * ( N - 1)计算得到RowNum。对RowNum向下取整得到FloorRowNum，对RowNum向上取整得到CeilRowNum。最终函数的结果有如下情况：

- 若FloorRowNum = RowNum = CeilRowNum，则返回结果为组内已排序好的第RowNum行排序键的值。

- 若三者不相等，组内第FloorRowNum行排序键的值记为FloorValue，组内第CeilRowNum行排序键的值记为CeilValue，则返回结果为FloorValue + (RowNum - FloorRowNum) * (CeilValue - FloorValue)。

**DESC|ASC与NULLS FIRST|LAST**

排序键可以指定升降序，ASC为升序，DESC为降序，默认为升序。

NULLS FIRST和NULLS LAST分别表示组内的NULL排在最前面和最后面。默认排在最大位置，即ASC时，默认为NULLS LAST；当DESC时，默认为NULLS FIRST。

示例（HEAP表）

```sql
-- 创建exprs表，并插入数据
DROP TABLE IF EXISTS exprs;
CREATE TABLE exprs(id INT , data INT, key_group INT);
INSERT INTO exprs VALUES(1,3, 1);
INSERT INTO exprs VALUES(2,1, 1);
INSERT INTO exprs VALUES(3,2, 1);
INSERT INTO exprs VALUES(4,4, 2);
COMMIT;
-- 计算data的中位数（即百分比为0.5的时候）
SELECT PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY data) res FROM exprs;
RES
----------------------------------------------------------------
2.5

-- 计算data中百分比为40%的插值
SELECT PERCENTILE_CONT(0.4) WITHIN GROUP (ORDER BY data) res FROM exprs;
RES
----------------------------------------------------------------
2.2

-- 使用group by进行分组，计算每组的中位数
SELECT key_group, PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY data) res FROM exprs GROUP BY key_group;
    KEY_GROUP         RES
------------ -----------
           1           2
           2           4
```

**OVER**

当指定OVER关键字时，PERCENTILE_CONT将作为[窗口函数](00内置函数（yashan模式）.html#WindowFunction)，将每个分组窗口内的行根据排序规则进行排序并计算对应百分比的插值，分组窗口内每一行的结果都相同。

**query_partition_clause**

窗口函数通用语法。

示例（HEAP表）

```sql
-- 创建exprs表，并插入数据
DROP TABLE IF EXISTS exprs;
CREATE TABLE exprs(id INT , data INT, key_group INT);
INSERT INTO exprs VALUES(1,3, 1);
INSERT INTO exprs VALUES(2,1, 1);
INSERT INTO exprs VALUES(3,2, 1);
INSERT INTO exprs VALUES(4,4, 2);
COMMIT;

-- 窗口函数计算每一个窗口内的中位数
SELECT id,data,key_group,
PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY data) OVER (PARTITION BY key_group) c1
FROM exprs ORDER BY id;
           ID         DATA    KEY_GROUP          C1
------------ ------------ ------------ -----------
           1            3            1           2
           2            1            1           2
           3            2            1           2
           4            4            2           4
```
