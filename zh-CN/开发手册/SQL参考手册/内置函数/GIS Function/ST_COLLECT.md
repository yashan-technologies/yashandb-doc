```ebnf
st_collect = ST_COLLECT "(" geometry1 "," geometry2 ")".
```
```ebnf
st_collect = ST_COLLECT "(" geomFiled ")".
```

ST_COLLECT函数的功能是对输入的一组geometry进行聚合，根据该组geometry是否具有相同或不同的类型，生成一个GEOMETRYCOLLECTION或MULTI*的geometry。

ST_COLLECT有两种形式，当入参为两个geometry参数时该函数作为普通函数，当入参为一组geometry字段时该函数作为聚合函数。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

输入的一组geometry须具有相同的空间参考系标识号（SRID）和相同的空间维度，否则报错。

本函数遵守如下规则：

* 当输入的参数全部为NULL时，函数返回NULL。
* 当输入的geometry全部为相同原子类型的geometry时，会返回MULTI*的geometry，否则返回GEOMETRYCOLLECTION。
* 本函数支持3D坐标的计算。
* 该函数要求输入的geometry的维度相同，否则报错。 
* 该函数要求输入的geometry的SRID相同，否则报错。
* geometry不能做为GROUP BY列。
* 该函数无法使用DISTINCT和ALL。
* 该函数无法指定OVER关键字去作为窗口函数使用。

示例（HEAP表）

```sql
-- 1.通过普通函数对两个GEOMETRY进行聚合
SELECT ST_ASTEXT(ST_COLLECT(ST_GEOMFROMTEXT('POINT(1 2)'), ST_GEOMFROMTEXT('POINT(3 4)')), 0) res FROM DUAL;

RES
---------------------------------------------------------------- 
MULTIPOINT (1 2, 3 4)

-- 2.创建表
CREATE TABLE geom(id INT, col_geom GEOMETRY);
INSERT INTO geom VALUES(1, ST_GEOMFROMTEXT('POINT(1 2)'));
INSERT INTO geom VALUES(1, ST_GEOMFROMTEXT('LINESTRING(3 4, 5 2)'));

-- 3.通过聚合函数对表中的GEOMETRY进行聚合
SELECT ST_ASTEXT(ST_COLLECT(col_geom), 0) res FROM geom;

RES
---------------------------------------------------------------- 
GEOMETRYCOLLECTION (POINT (1 2), LINESTRING (3 4, 5 2))
```
