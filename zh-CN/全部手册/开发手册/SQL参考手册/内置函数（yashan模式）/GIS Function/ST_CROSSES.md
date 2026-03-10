```ebnf+diagram
st_crosses::= ST_CROSSES "(" geometry1 "," geometry2 ")"
```

ST_CROSSES函数的功能是判断两个Geometry是否有部分（非全部）相同的内点。

如它们的交点“在空间上交叉”，即两个Geometry有部分（非全部）内部点共有，交叉则返回TRUE，否则返回FALSE。

交叉需满足如下两个条件：

- 两个Geometry内部的交集必须是非空的，且维度须小于两个输入的Geometry的最大维度。
- 两个Geometry的交集不能等于输入的Geometry中的任何一个。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

输入的geometry1和geometry2须具有相同的空间参考系标识号（SRID）。

本函数遵守如下规则：

* 当输入的参数存在NULL时，函数返回NULL。
* 仅计算2D结果，若输入参数中存在Z坐标，函数将直接忽略Z坐标进行计算。
* 能够保证的精度是小数点后面15位，小数部分超出15位之后结果不保证。
* 遵循DE-9IM（Dimensionally Extended 9-Intersection Model）规则。

如两个Geometry的DE-9IM交叉矩阵匹配以下情况，则此关系成立：

* T\*T******：对于Point/Line，Point/Area和Line/Area的场景。
* T***\*\*T**：对于Line/Point，Area/Point和Area/Line的场景。
* 0********：对于Line/Line的场景。

对于Point/Point和Area/Area的场景始终返回FALSE。

示例（HEAP表）

```sql
--ST_GEOMFROMTEXT函数会根据给定的WKT和SRID返回一个ST_GEOMETRY数据
SELECT ST_Crosses(ST_GeomFromText('LINESTRING(3 5, 1 2, 3 5)'), ST_GeomFromText('LINESTRING(3 5, 4 6 ,3 5)')) res FROM DUAL;

RES 
-------------------- 
true                
                              
SELECT ST_Crosses(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), ST_GeomFromText('LINESTRING(3 5, 4 6, 3 5)')) res FROM DUAL;

RES
-------------------- 
false               

SELECT ST_Crosses(ST_GeomFromText('POLYGON((2 2, 2 4, 4 4, 4 2, 2 2))'), NULL) res FROM DUAL;

RES 
-------------------- 
                    
```
