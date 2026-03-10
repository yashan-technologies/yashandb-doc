```ebnf+diagram
st_buffer::= ST_BUFFER "(" geometry "," width [ "," style ] ")"
```

ST_BUFFER函数的功能是返回一个ST_GEOMETRY类型数据，该数据覆盖从输入的geometry到给定的距离width内的所有点，实际上得到的计算结果始终是一个有效的POLYGON数据。

当输入的参数存在NULL时，函数返回NULL。

**geometry**

[通用表达式](../../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据，遵循如下规则：

* 仅支持计算2D结果。如geometry的坐标中有Z轴，则忽略Z坐标进行计算，计算结果仍然是一个2D的ST_GEOMETRY数据。
* 如输入的是一个EMPTY的ST_GEOMETRY数据，则会返回POLYGON EMPTY（如果一个GEOMETRYCOLLECTION只有其中一部分是EMPTY，则计算结果不一定是POLYGON EMPTY）。
* 输入的geometry的坐标中不允许含有非法数字（如inf、nan），否则报错。

**width**

width用于规定距离，其值为DOUBLE类型，遵循如下规则：

* 支持能够隐式转换成DOUBLE的数据类型。
* 如果输入的width是一个负值，则会缩小该geometry，极端情况下会使结果的POLYGON数据缩小为0，从而返回POLYGON EMPTY；对于POINT和LINESTRING类型而言，如果width是负值，则始终返回POLYGON EMPTY。
* width的单位是输入的geometry的空间参考系的单位。

**style**

style用于控制结果的精度和样式，其值为VARCHAR类型。该参数可以省略，此时会对精度和样式设置默认值。

* 支持能够隐式转换成VARCHAR的数据类型。
* style有五个参数可以设置，可通过`key=value`的方式指定，不同键值对之间使用空格进行分割，键值对的顺序没有限制，且无视大小写，部分键值设有别名，具体规则如下：
  * `quad_segs=#` : 表示四分之一圆有几个片段（线段），默认值为8，小于0时取0，最大值为262144，超过最大值则按最大值计算。如输入的是小数，则会截断成整数；如输入的是无效的数据，则转换成0；如计算结果长度超过65534，则报错。
  * `endcap=round|flat(butt)|square` : 端点样式，默认值为round。
  * `mitre_limit(miter_limit)=#.#` : 用于限制斜接比率，只能影响`join=mitre(miter)`的情况，默认值为5.0。
  * `join=round|mitre(miter)|bevel`：连接样式，默认值为round。
  * `side=both|left|right` : left和right表示形成一个单边的图形，仅影响LINESTRING类型，不影响POINT或者POLYGON类型（仍形成封闭的ST_GEOMETRY数据），both则会形成一个封闭的ST_GEOMETRY数据，默认值为both。

示例（HEAP表）

```sql
--ST_GEOMFROMTEXT函数会根据给定的WKT和SRID返回一个ST_GEOMETRY数据
SELECT ST_AsText(ST_Buffer(ST_GeomFromText('POLYGON((50 50, 150 150, 150 50, 50 50))'), -2, 'quad_segs=1'), 0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
POLYGON ((55 52, 148 145, 148 52, 55 52))                       

SELECT ST_AsText(ST_Buffer(ST_GeomFromText('POLYGON((50 50, 150 150, 150 50, 50 50))'), 1, 'quad_segs=1'), 0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
POLYGON ((50 49, 49 50, 49 51, 149 151, 150 151, 151 150, 151 50, 150 49, 50 49))
    
SELECT ST_AsText(ST_Buffer(ST_GeomFromText('LINESTRING(1 3 5, 2 4 6, 1 3 5)'), 1, 'quad_segs=2 join=bevel'), 0) res FROM DUAL;

RES                                            
---------------------------------------------------------------- 
POLYGON ((0 3, 0 4, 1 5, 3 3, 2 2, 1 2, 0 2, 0 3))              

SELECT ST_AsText(ST_Buffer(ST_GeomFromText('LINESTRING(1 3 5, 2 4 6, 1 3 5)'), 1, 'quad_segs=2'), 0) res FROM DUAL;

RES                                            
---------------------------------------------------------------- 
POLYGON ((0 3, 0 4, 1 5, 2 5, 3 5, 3 4, 3 3, 2 2, 1 2, 0 2, 0 3))

SELECT ST_AsText(ST_Buffer(ST_GeomFromText('POINT(100 90 100)'), 1, 'quad_segs=1'), 0) res FROM DUAL;

RES                                            
---------------------------------------------------------------- 
POLYGON ((101 90, 100 89, 99 90, 100 91, 101 90))               
```
