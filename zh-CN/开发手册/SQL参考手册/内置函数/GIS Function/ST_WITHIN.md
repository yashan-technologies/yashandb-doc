```ebnf
st_within = ST_WITHIN "(" geometry1 "," geometry2 ")".
```

ST_WITHIN函数的功能是判断geometry1是否完全在geometry2的内部，如果是则返回TRUE，否则返回FALSE。

geometry1在geometry2内部指当且仅当geometry1中没有点位于geometry2的外部，且geometry1的内部至少有一个点位于geometry2的内部的情况。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

输入的geometry1和geometry2须具有相同的空间参考系标识号（SRID）。

本函数遵守如下规则：

* ST_WITHIN是ST_CONTAINS的逆。因此，ST_WITHIN(A,B) = ST_CONTAINS(B,A)。 
* 当输入的参数存在NULL时，函数返回NULL。 
* 仅计算2D结果，若输入参数中存在Z坐标，函数将直接忽略Z坐标进行计算。 
* 能够保证的精度是小数点后面15位，小数部分超出15位之后结果不保证。 
* 遵循DE-9IM（Dimensionally Extended 9-Intersection Model）规则。

示例（HEAP表）

```sql
--ST_GEOMFROMTEXT函数会根据给定的WKT和SRID返回一个ST_GEOMETRY数据
SELECT ST_Within(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), ST_GeomFromText('POINT(3 3)')) res FROM DUAL;

RES 
-------------------- 
false                
                                               
SELECT ST_Within(ST_GeomFromText('POINT(3 3)'), ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))')) res FROM DUAL;

RES
-------------------- 
true                
    
SELECT ST_Within(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), NULL) res FROM DUAL;

RES
-------------------- 
                    
```
