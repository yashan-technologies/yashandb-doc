```ebnf+diagram
st_maxdistance::= ST_MAXDISTANCE "(" geometry1 "," geometry2 ")"
```

ST_MAXDISTANCE函数根据输入的geometry1和geometry2，返回它们对应的二维最大距离。

**geometry**

[通用表达式](../../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据，遵循如下规则：

* geometry1和geometry2的空间参考系标识号（SRID）必须相等，否则报错。

本函数遵守如下规则：

* 当输入的参数存在NULL或EMPTY时，函数返回NULL。
* 仅计算2D结果，若输入参数中存在Z坐标，函数将直接忽略Z坐标进行计算。

示例（HEAP表）

```sql
SELECT ST_MaxDistance(ST_GeomFromText('linestring(-72.1523 42.6343, -72.4524 42.2872)', 4326), ST_GeomFromText('linestring(-72.4524 42.4526, -72.1235 42.3521)',4326)) res FROM dual;

RES
----------- 
3.508E-001

SELECT ST_MaxDistance(ST_Point(-72.1235, 42.3521, 4326), ST_GeomFromText('polygon((-72.1260 42.45, -72.123 42.1546, -72.1244 42.3527, -72.1260 42.45))',4326)) res FROM dual;

RES
----------- 
1.975E-001
```
