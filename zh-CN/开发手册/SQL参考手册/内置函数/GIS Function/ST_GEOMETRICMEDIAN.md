```ebnf
st_geometricmedian = ST_GEOMETRICMEDIAN "(" geometry [ "," tolerance ] [ "," maxIter ] [ "," failIfNotConverged ] ")".
```

ST_GEOMETRICMEDIAN函数的功能是使用Weiszfeld算法计算MULTIPOINT数据的几何中位数。最终会返回一个POINT数据，该POINT到MULTIPOINT的所有POINT的距离之和是最小的。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据，遵循如下规则：

* 当该参数为NULL时，函数返回NULL。
* 输出的POINT数据维度由输入的MULTIPOINT数据的最大维度决定，且不会超过三维。
* 输入geometry的是POINT类型时，则会返回该POINT，输入的是MULTIPOINT EMPTY时则返回POINT EMPTY，输入其他类型则报错。

**tolerance**

tolerance表示容差，其值为DOUBLE类型。Weiszfeld算法会不断迭代进行计算，直到连续迭代之间的距离变化小于给定的容差（tolerance），该参数遵循如下规则：

* 该参数可以省略，如省略或输入NULL，则会根据输入的geometry的外包框计算一个tolerance。
* 支持能够隐式转换成DOUBLE的数据类型。
* tolerance仅支持为非负数，否则报错。

**maxIter**

maxIter表示迭代最大次数，其值为INT类型，遵循如下规则：

* 该参数可以省略，默认值为10000。
* 支持能够隐式转换成INT的数据类型。
* maxIter只能是正整数，输入的是负数或者是Null则报错。
* 当failIfNotConverged参数为TRUE时，如进行了maxIter次迭代仍未满足第二个条件，则会报错。
* 当failIfNotConverged参数为FALSE时，则即使进行了maxIter次迭代仍未满足第二个条件，也不会报错。

**failIfNotConverged**

failIfNotConverged表示迭代计算次数超过maxIter是否报错，其值为BOOLEAN类型，遵循如下规则：

* 该参数可以省略，默认值为FALSE，输入NULL同样视作FALSE。
* 支持能够隐式转换成BOOLEAN的数据类型。

示例（HEAP表）

```sql
--ST_GEOMFROMTEXT函数会根据给定的WKT和SRID返回一个ST_GEOMETRY数据
SELECT ST_AsText(ST_GeometricMedian(ST_GeomFromText('MULTIPOINT(1 1, 2 2, 3 3)')), 0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
POINT (2 2)                                                     

SELECT ST_AsText(ST_GeometricMedian(ST_GeomFromText('MULTIPOINT(1 1, 2 2, 3 3 3)')), 0) res FROM DUAL;

RES                                            
---------------------------------------------------------------- 
POINT Z (2 2 0)                                                 

SELECT ST_AsText(ST_GeometricMedian(ST_GeomFromText('MULTIPOINT(1 1, 2 2, 3 3 3)'), NULL, 0, TRUE), 0) res FROM DUAL;

YAS-07202 plugin execution error, Median failed to converge within 1e-08 after 0 iterations.
```
