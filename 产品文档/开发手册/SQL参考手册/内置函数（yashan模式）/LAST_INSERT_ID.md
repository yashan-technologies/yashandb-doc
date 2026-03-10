```ebnf+diagram
last_insert_id::= LAST_INSERT_ID ["(" [expr] ")"]
```

LAST_INSERT_ID函数用于获取本次会话中最新的自增值或用于将参数[expr](../通用SQL语法/expr)转换为BIGINT类型。

本函数不支持向量化计算。

**获取自增值**

当LAST_INSERT_ID函数无入参时，用于获取本次会话中最新的自增值。自增相关概念如下：

- 自增列：表中首个默认值为sequence的索引列。当为联合索引时，该列需为索引声明的第一列。每张表至多有一个自增列。

- 自增：每次执行INSERT语句但不显式插入自增列时触发自增。

- 自增值：每个会话的自增初始值为0，当前会话中触发过自增后，自增值取值规则如下：

  - 若最近一次执行INSERT语句时插入多行数据，自增值 = 第一行数据插入成功后自增列对应的值。

  - 若最近一次执行INSERT语句时插入1行数据，自增值 = 插入成功后自增列对应的值。

LAST_INSERT_ID()返回当前会话最新的自增值，返回类型为BIGINT。若溢出，则返回0。

**转换类型**

当LAST_INSERT_ID函数存在一个参数expr时，用于将参数转换为BIGINT类型并输出，同时会更新当前会话的自增值为LAST_INSERT_ID(expr)的返回值或0。

- expr不能为UDT类型。

- 若expr转换BIGINT类型失败，则LAST_INSERT_ID(expr)返回0。

- 若expr为NULL，LAST_INSERT_ID(expr)返回NULL且会重置当前会话的自增值为初始值0，即执行LAST_INSERT_ID(NULL)查询后再执行LAST_INSERT_ID()查询，返回值将为0。

示例（HEAP表）

```sql
--在session中，初始值为0
SELECT LAST_INSERT_ID() res FROM DUAL;

RES
---------------------
0

SELECT LAST_INSERT_ID(100) res FROM DUAL;

RES
---------------------
100

--无参数查询受有参数查询影响，返回前次有参数值参数结果
SELECT LAST_INSERT_ID() res FROM DUAL;

RES
---------------------
100

SELECT LAST_INSERT_ID(NULL) res FROM DUAL;

RES
---------------------


--参数为NULL时，返回NULL，紧接着无参数查询将返回0
SELECT LAST_INSERT_ID() res FROM DUAL;

RES
---------------------
0

CREATE SEQUENCE seq_area;
CREATE TABLE area_info (id INT DEFAULT seq_area.NEXTVAL PRIMARY KEY, area_name VARCHAR(20));
INSERT INTO area_info(area_name) VALUES('广东');
INSERT INTO area_info(area_name) VALUES('浙江'),('陕西');
COMMIT;

SELECT id, area_name FROM area_info;

ID AREA_NAME
------------ ---------------------
           1 广东
           2 浙江
           3 陕西

--多行插入时，返回第一行触发自增数据的对应值
SELECT LAST_INSERT_ID() RES FROM DUAL;

RES
---------------------
2
```

