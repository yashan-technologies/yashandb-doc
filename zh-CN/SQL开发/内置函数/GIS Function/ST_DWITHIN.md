```ebnf+diagram
st_dwithin::= ST_DWITHIN "(" geometry1 "," geometry2 "," distance ")"
```

ST_DWITHIN函数的功能是判断geometry1与geometry2是否在给定的距离distance内，如果在distance内则返回TRUE，否则返回FALSE。

**geometry**

[通用表达式](../../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

输入的geometry1和geometry2须具有相同的空间参考系标识号（SRID）。

**distance**

distance的数据类型是DOUBLE，表示指定的距离。

* 支持能够隐式转换成DOUBLE的类型。
* 如果输入的distance小于0，则报错。

本函数遵守如下规则：

* 当输入的参数存在NULL时，函数返回NULL。
* 若输入的任意一个geometry为EMPTY，函数返回FALSE。
* 仅计算2D结果，若输入参数中存在Z坐标，函数将直接忽略Z坐标进行计算。
* geometry的空间参考系标识号（SRID）必须在spatial_ref_sys系统表中定义或者为0，否则报错。
* geometry的空间参考系标识号（SRID）在spatial_ref_sys中对应的srs_type如果是GEOGRAPHY2D或者GEOGRAPHY3D，会切换到大地坐标算法计算，否则会使用投影坐标算法。
* 该函数支持RTree索引，但不建议在经纬度坐标系下使用该函数的RTree索引。

示例（HEAP表）

```sql
--返回两个geometry之间的距离
SELECT ST_Distance(ST_GeomFromText('POINT(0 0)'), ST_GeomFromText('POINT(3 4)')) res FROM DUAL;

RES
----------- 
5.0E+000

--两个geometry之间的距离在指定的distinct内
SELECT ST_DWithin(ST_GeomFromText('POINT(0 0)'), ST_GeomFromText('POINT(3 4)'), 5) res FROM DUAL;

RES
-------------------- 
true                

--两个geometry之间的距离不在指定的distinct内
SELECT ST_DWithin(ST_GeomFromText('POINT(0 0)'), ST_GeomFromText('POINT(3 4)'), 4) res FROM DUAL;

RES
-------------------- 
false
```
