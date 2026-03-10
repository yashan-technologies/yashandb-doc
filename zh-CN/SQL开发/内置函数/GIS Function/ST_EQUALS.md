```ebnf+diagram
st_equals::= ST_EQUALS "(" geometry1 "," geometry2 ")"
```

ST_EQUALS函数的功能是判断两个Geometry是否包含同一组点，即给定的两个Geometry“空间相等”，相等则返回TRUE，否则返回FALSE。

空间相等指`ST_WITHIN（A，B）= TRUE`和`ST_WITHIN（B，A）= TRUE`，也意味着点的顺序可以不同，但表示相同的几何结构。

**geometry**

[通用表达式](../../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

输入的geometry1和geometry2须具有相同的空间参考系标识号（SRID）。

本函数遵守如下规则：

* 当输入的参数存在NULL时，函数返回NULL。
* 仅计算2D结果，若输入参数中存在Z坐标，函数将直接忽略Z坐标进行计算。
* 能够保证的精度是小数点后面15位，小数部分超出15位之后结果不保证。
* 遵循DE-9IM（Dimensionally Extended 9-Intersection Model）规则。

示例（HEAP表）

```sql
--ST_GEOMFROMTEXT函数会根据给定的WKT和SRID返回一个ST_GEOMETRY数据
SELECT ST_Equals(ST_GeomFromText('LINESTRING(2 2, 1 1, 3 3)'), ST_GeomFromText('LINESTRING(1 1, 2 2, 3 3)')) res FROM DUAL;

RES 
-------------------- 
true                

SELECT ST_Equals(ST_GeomFromText('LINESTRING(2 2, 1 1, 3 3)'), ST_GeomFromText('LINESTRING(3 5, 4 6 ,3 5)')) res FROM DUAL;

RES 
-------------------- 
false               

SELECT ST_Equals(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), NULL) res FROM DUAL;

RES 
-------------------- 
                    
```
