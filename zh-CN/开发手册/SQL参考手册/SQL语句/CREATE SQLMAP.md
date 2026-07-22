通用描述
----

CREATE SQLMAP用于创建一个SQL映射，通过将A语句映射为B语句，实现数据库接收A语句请求而实际执行B语句的能力。此功能可作为SQL调优的辅助方法，在某些优化器没有选取到最优执行计划的特殊场景中，通过映射表将特定SQL替换为等价SQL，以使数据库按最优计划执行。 

执行本语句需注意如下事项：

- 用户必须拥有DBA权限才能创建一个SQL映射。
- 要映射的SQL语句类型必须是DML语句。
- 系统参数SQL_MAP（默认值`FALSE`）为true时映射才会生效，为false时本语句可以执行成功，但映射并不会生效。
- 本语句不会校验原始语句和映射语句的正确性，输入错误的语句将导致执行时报错或输出非预期的结果。

在存算一体分布式集群部署中，执行本语句时系统会检查是否存在异常未恢复DDL语句，若存在将报错并需等待对应DDL恢复成功后才能执行本语句。

语句定义
----

**create\_sqlmap::=**

```ebnf
= CREATE SQLMAP map_name "(" user_name "," "'sql'" "," "'map_sql'"  ")" .
```

### map\_name

将要创建的SQL映射的名称，该名称全局唯一，不能与已有SQL映射名称重复，不能带模式名指定，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符.md)。

### user\_name

指定SQL映射对应的用户名，可为如下值：

- ALL：表示该映射对所有用户都生效。
- CURRENT_USER：表示该映射对当前的用户生效。
- 数据库中存在的用户名，对于使用双引号命名创建的用户，需为其实际大小写名称，否则不区分大小写。

### sql

原始语句，如果指定的语句已经被创建过映射，不能再次创建。

### map_sql

映射语句，必须与原始语句是相同的SQL语句类型，例如都为SELECT语句。

示例

```sql
-- 创建一个SQL映射，将查询所有机构的语句进行优化，改为获取区域有效的机构，且只获取有用的字段
CREATE SQLMAP map_branch (sales,'SELECT * FROM branches','select branch_no,branch_name from branches where area_no in (select area_no from area)');

-- sales用户执行SELECT * FROM branches时的效果
SELECT * FROM branches;
BRANCH_NO BRANCH_NAME                                                      
--------- ----------------------------
0101      Shanghai                                                          
0102      Nanjing                                                          
0103      Fuzhou                                                          
0104      Xiamen                                                          
0401      Beijing                                                          
0402      Tianjin                                                          
0403      Dalian                                                          
0404      Shenyang                                                          
0201      Chengdu                                                          
0502      Changsha   

-- 不能为原始语句创建多个映射
CREATE SQLMAP map_branch (all,'SELECT * FROM branches','select branch_no,branch_name from branches where area_no in (select area_no from area)');
[1:31]YAS-04398 duplicate sql
```
