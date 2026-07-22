```ebnf
st_contains = ST_CONTAINS "(" geometry1 "," geometry2 ")".
```

ST_CONTAINS函数的功能是判断geometry1是否包含geometry2，包含时返回TRUE，否则返回FALSE。

geometry1包含geometry2指当且仅当geometry2中没有点位于geometry1的外部，且geometry2的内部至少有一个点位于geometry1的内部的情况。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

输入的geometry1和geometry2须具有相同的空间参考系标识号（SRID）。

本函数遵守如下规则：

* ST_CONTAINS是ST_WITHIN的逆。因此，ST_CONTAINS(A,B) = ST_WITHIN(B,A)。 
* 当输入的参数存在NULL时，函数返回NULL。 
* 仅计算2D结果，若输入参数中存在Z坐标，函数将直接忽略Z坐标进行计算。 
* 能够保证的精度是小数点后面15位，小数部分超出15位之后结果不保证。 
* 遵循DE-9IM（Dimensionally Extended 9-Intersection Model）规则。

示例（HEAP表）

```sql
--ST_GEOMFROMTEXT函数会根据给定的WKT和SRID返回一个ST_GEOMETRY数据
SELECT ST_Contains(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), ST_GeomFromText('POINT(3 3)')) res FROM DUAL;

RES 
-------------------- 
true                
                                              
SELECT ST_Contains(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), ST_GeomFromText('POINT(1 2)')) res FROM DUAL;

RES 
-------------------- 
false                
    
SELECT ST_Contains(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), NULL) res FROM DUAL;

RES 
-------------------- 
                    
```
