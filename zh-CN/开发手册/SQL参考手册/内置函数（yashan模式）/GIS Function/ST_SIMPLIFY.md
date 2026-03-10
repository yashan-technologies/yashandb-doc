```ebnf+diagram
st_simplify::= ST_SIMPLIFY "(" geometry "," tolerance ")"
```

ST_SIMPLIFY函数的功能是使用Douglas-Peucker算法来简化输入的geometry。

当输入的参数存在NULL时，函数返回NULL。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据，遵循如下规则：

* 仅计算2D结果，如坐标中有Z轴，则会忽略Z坐标进行计算。
* 如输入的geometry是POINT、MULTIPOINT类型，则直接返回原来的值。
* 如输入的geometry是LINESTRING、MULTILINESTRING类型，除非输入的是LINESTRING EMPTY或者是MULTILINESTRING EMPTY，才会返回EMPTY，否则至少保留两个点。
* 如输入的geometry是POLYGON、MULTIPOLYGON类型，缩小到一定程度之后会返回POLYGON EMPTY或MULTIPOLYGON EMPTY。
* 如输入的geometry是GEOMETRYCOLLECTION类型，内部的各类数据变化与上述一致。

**tolerance**

tolerance表示容差，其值为DOUBLE类型，容差越大，则简化的程度越大，遵循如下规则：

* 支持能够隐式转换成DOUBLE的数据类型。
* 如输入负数，则转化成对应的正数进行计算。
* tolerance值过大时，输入的geometry可能会消失（变成EMPTY）。

示例（HEAP表）

```sql
--ST_GEOMFROMTEXT函数会根据给定的WKT和SRID返回一个ST_GEOMETRY数据
SELECT ST_AsText(ST_Simplify(ST_GeomFromText('LINESTRING (3 5, 4 6, 2 1, 3 5)'), 100), 0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
LINESTRING (3 5, 3 5)                                           

SELECT ST_AsText(ST_Simplify(ST_GeomFromText('POLYGON ((0 0, 1 0, 1 1, 0 1, 0 0))'), 0.5), 0) res FROM DUAL;

RES                                          
---------------------------------------------------------------- 
POLYGON ((0 0, 1 0, 1 1, 0 1, 0 0))                             

SELECT ST_AsText(ST_Simplify(ST_GeomFromText('POLYGON ((0 0, 1 0, 1 1, 0 1, 0 0))'), 1), 0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
POLYGON EMPTY                                                   

SELECT ST_AsText(ST_Simplify(ST_GeomFromText('POLYGON ((0 0, 1 0, 1 1, 0 1, 0 0))'), NULL), 0) res FROM DUAL;

RES                                            
---------------------------------------------------------------- 
```
