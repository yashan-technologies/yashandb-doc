```ebnf+diagram
st_extent::= ST_EXTENT "(" geomFiled ")"
```

ST_EXTENT函数的功能是返回一个包围一组geometry的二维边界框，是一个聚合函数，返回类型是BOX2D。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

本函数遵守如下规则：

* 当输入的一组geometry全部为NULL或全部为EMPTY时，该函数返回NULL。
* 本函数只计算2D结果，如果坐标中有Z轴将直接忽略Z坐标进行计算。
* geometry不能做为GROUP BY列。
* 该函数无法使用DISTINCT和ALL。
* 该函数无法指定OVER关键字去作为窗口函数使用。

示例（HEAP表）

```sql
-- 1.创建表
DROP TABLE IF EXISTS geom;
CREATE TABLE geom(id INT, col_geom GEOMETRY);
INSERT INTO geom VALUES(1, ST_GEOMFROMTEXT('POINT(1 2)'));
INSERT INTO geom VALUES(1, ST_GEOMFROMTEXT('LINESTRING(3 4, 5 2)'));

-- 2.创建BOX2D的输出函数
CREATE OR REPLACE FUNCTION BOX2D_OUT(box BOX2D) RETURN VARCHAR AS
    res VARCHAR(100);
BEGIN
    res := 'BOX(' || box.xmin || ' ' || box.xmax || ',' || box.ymin || ' ' || box.ymax || ')';
RETURN res;
END;
/
    
-- 3.通过聚合函数对表中的GEOMETRY进行聚合
SELECT BOX2D_OUT(ST_EXTENT(col_geom)) res FROM geom;

RES
---------------------------------------------------------------- 
BOX(1.0E+000 5.0E+000,2.0E+000 4.0E+000)
```
