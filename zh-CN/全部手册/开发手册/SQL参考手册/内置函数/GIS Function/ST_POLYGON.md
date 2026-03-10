```ebnf+diagram
st_polygon::= ST_POLYGON "(" geometry "," srid ")"
```

ST_POLYGON函数根据输入的geometry和srid，返回对应srid中由geometry组成的POLYGON数据。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据，遵循如下规则：

* geometry须是LINESTRING类型。
* geometry包含的POINT数量须大于或等于4。
* geometry的首尾2个POINT必须相同。

**srid**

srid的数据类型是INT，表示输出结果中的空间参考系，遵循如下规则：

*   支持能够隐式转换成INT的类型，如输入小数则进行四舍五入转换。
*   如果输入的是负数，则会按照geometry本身的空间参考系标识号（SRID）进行计算。

当输入的参数存在NULL时，函数返回NULL。

示例（HEAP表）

```sql
--ST_GEOMFROMTEXT函数会根据给定的WKT和SRID返回一个ST_GEOMETRY数据
SELECT ST_AsText(ST_Polygon(ST_GeomFromText('LINESTRING(0 0, 1 1, 2 3, 0 0 )'), 0), 0) res FROM DUAL;

RES                                            
---------------------------------------------------------------- 
POLYGON ((0 0, 1 1, 2 3, 0 0))    

SELECT ST_AsText(ST_Polygon(ST_GeomFromText('LINESTRING(0 0 0, 1 1 1, 2 3 4, 0 0 0)'), 4326), 0) res FROM DUAL;

RES                                           
---------------------------------------------------------------- 
POLYGON Z ((0 0 0, 1 1 1, 2 3 4, 0 0 0))

--SRID为负数时按照geometry本身的SRID计算
SELECT ST_SRID(ST_Polygon(ST_GeomFromText('LINESTRING(0 0 0, 1 1 1, 2 3 4, 0 0 0)',5111), -4326)) res FROM DUAL;

         RES 
------------ 
        5111  

SELECT ST_SRID(ST_Polygon(ST_GeomFromText('LINESTRING(0 0 0, 1 1 1, 2 3 4, 0 0 0)'), 4326)) res FROM DUAL;

         RES 
------------ 
        4326
```
