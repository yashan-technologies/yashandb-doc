```ebnf+diagram
st_relate::= ST_RELATE "(" geometry1 "," geometry2 [ "," boundaryNodeRule ] ")"
```

ST_RELATE函数的功能是根据输入的边界值规则(boundaryNodeRule)，计算用于表示输入的两个Geometry之间空间关系的DE-9IM矩阵字符串。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

输入的geometry1和geometry2须具有相同的空间参考系标识号（SRID）。

**boundaryNodeRule**

boundaryNodeRule表示边界值规则，该参数为INTEGER类型，支持能够隐式转换成INTEGER类型的数据，默认值是1，可选参数有以下四个（输入其他值则报错）：
* 1: OGC/MOD2。
* 2: Endpoint。
* 3: MultivalentEndpoint。
* 4: MonovalentEndpoint。

本函数遵守如下规则：

* 当输入的参数存在NULL时，函数返回NULL。
* 仅计算2D结果，若输入参数中存在Z坐标，函数将直接忽略Z坐标进行计算。
* 能够保证的精度是小数点后面15位，小数部分超出15位之后结果不保证。
* 遵循DE-9IM（Dimensionally Extended 9-Intersection Model）规则。

示例（HEAP表）

```sql
--ST_GEOMFROMTEXT函数会根据给定的WKT和SRID返回一个ST_GEOMETRY数据
SELECT ST_Relate(ST_GeomFromText('LINESTRING(2 2, 1 1, 3 3)'), ST_GeomFromText('LINESTRING(3 5, 2 2, 3 3)')) FROM DUAL;

ST_RELATE(ST_GEOMFRO                                             
---------------------------------------------------------------- 
1F100F102                                                       

SELECT ST_Relate(ST_GeomFromText('POINT(1 2)'), ST_GeomFromText('LINESTRING(3 5, 2 2, 3 3)')) FROM DUAL;

ST_RELATE(ST_GEOMFRO                                             
---------------------------------------------------------------- 
FF0FFF102                                                       

SELECT ST_Relate(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), NULL) FROM DUAL;

ST_RELATE(ST_GEOMFRO                                             
---------------------------------------------------------------- 
                                                                
```
