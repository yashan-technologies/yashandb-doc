```ebnf
st_makeenvelope = ST_MakeEnvelope "("xmin", "ymin", "xmax", "ymax"["srid"])".
```

ST_MAKEENVELOPE函数用于返回一个根据输入的X、Y的最小、最大值构建的外包矩形。其结果为ST_Geometry类型的Polygon。

本函数遵守如下规则：

- 当xmin、ymin、xmax、ymax中有NULL时，返回结果为NULL。
- 当srid为NULL时，返回结果为NULL。

**xmin**

[通用表达式](../../通用SQL语法/expr)，类型为double。

**ymin**

[通用表达式](../../通用SQL语法/expr)，类型为double。

**xmax**

[通用表达式](../../通用SQL语法/expr)，类型为double。

**ymax**

[通用表达式](../../通用SQL语法/expr)，类型为double。

**srid**

[通用表达式](../../通用SQL语法/expr)，类型为integer，缺省值为0。

示例（HEAP表）

```sql
select st_astext(st_MakeEnvelope(1,2,3,4,4326), 0) geom from dual;

GEOM
----------------------------------------------------------------
POLYGON ((1 2, 3 2, 3 4, 1 4, 1 2))

select st_srid(st_MakeEnvelope(1,2,3,4,4326))  srid from dual;

SRID
------------
4326

select st_srid(st_MakeEnvelope(1,2,3,4)) srid from dual;

SRID
------------
0

select st_astext(st_MakeEnvelope(1,2,3,4,null), 0) geom from dual;

GEOM
----------------------------------------------------------------

select st_astext(st_MakeEnvelope(1,2,3,null), 0) geom from dual;

GEOM
----------------------------------------------------------------

```
