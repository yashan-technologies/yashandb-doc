Hint是一种特殊的注释，它以固定的格式和位置出现在SQL语句的文本中，用于影响优化器对于执行计划的选择。但这种影响不是强制的，优化器在某些情况下可能忽略SQL的hint指令。

Hint相关语法介绍请查阅[hint](../../../开发手册/SQL参考手册/通用SQL语法/hint)。

## 改变Join连接顺序的Hint

LEADING语法能够改变Join两边表的顺序，常见做法是通过LEADING调整大小表，减少扫描数量；或通过LEADING调整表连接顺序，增加选择率，减少执行负担。

LEADING有一定的限制，例如只能调整具体表的顺序，而不能调整表与表连接后产生的结果集与其他结果集之间的顺序。

## 改变Join类型的Hint

USE_和NO_USE_语法能够指定两张表的连接类型：

|  模式| 说明| 备注|
| :----------- | :---------------------------- | :------------------------------------------------- |
| USE_NL       | 优先选择Nesed Loop Join       | 任何Join类型一定能转成Nested Loop Join             |
| NO_USE_NL    | 优先选择非Nesed Loop Join类型 | 无法生成其他Join类型时，不生效                   |
| USE_HASH     | 优先使用Hash Join             | 无法使用Hash Join时，不生效                      |
| NO_USE_HASH  | 不使用 Hash Join              | 根据代价评估使用Nested Loop Join或Merge Join |
| USE_MERGE    | 优先使用Merge Join            | 无法生成Merge Join时，不生效                     |
| NO_USE_MERGE | 不使用Merge Join              | 根据评估使用Nested Loop Join或Hash Join        |

## 改变访问路径的Hint

Hint指令也可以改变访问路径，例如强制根据索引扫描或强制采用全表扫描。

|  模式| 说明| 备注|
| :----------- | :--------------------- | :--------------------------------------------------------- |
| INDEX        | 优先使用Hint指定的索引 | 一定会生效，选Fast Full Scan和Index Scan之间Cost最小的一个 |
| NO_INDEX     | 不使用Hint指定的索引   | 一定会生效，选择Table Full Scan                            |
| INDEX_FFS    | 优先使用Fast Full Scan | 一定会生效，只选Fast Full Scan                             |
| NO_INDEX_FFS | 不使用Fast Full Scan   | 一定会生效，可选Index Range Scan等                         |
| FULL         | 使用全表扫描           | 一定会生效，选择Table Full Scan                            |

## 改变查询并行度


使用PARALLEL提示项，可以实现并行查询。为了语法兼容，配置PARALLEL提示项时需使用hint_intersperse同时指定表名和并行度[表名,并行度]，但仍表示是对查询操作整体的并行。

若查询中有多张表同时通过hint指定多个并行度值时，所有表的并行度将全采用此次最大的指定值。

通过hint所指定的并行度最大为255，超过255时会退化为255。

无法跨SELECT指定并行度，此种场景请使用数据库参数DEGREE_OF_PARALLEL来指定。

对并行查询的指定只作为一个参考项，如果优化器评估后认为并行查询不是最优计划，则不会选择生成并行计划。

示例

```sql
SELECT /*+ PARALLEL(area,4)*/ area_no FROM area;

-- 通过hint对表a和表b指定不同的并行度（3、4）时，两个表的并行度将全为4
SELECT /*+ PARALLEL(a,3) PARALLEL(b,4)*/ a.area_no,b.branch_no
FROM area a,branches b
WHERE a.area_no = b.area_no;

-- 无效，无法跨SELECT指定并行度
SELECT /*+PARALLEL(branches,4)*/ area_no FROM area
UNION
SELECT area_no FROM branches;
```


DML和INSERT等SQL语句的并行配置要求请参考[hint参考](../../../开发手册/SQL参考手册/通用SQL语法/hint)。

## 改变批量模式


使用BATCH_MODE提示项时，无需指定表名，该提示项仅适用于单机部署中HEAP表的查询与插入语句，用于指定当前语句的批量执行模式。

示例（单机HEAP表）

```sql
SELECT /*+ BATCH_MODE(2) */*
FROM orders_info o, sales_info s,finance_info f
WHERE o.salesperson = s.salsperson
AND s.branch = f.branch;
```
