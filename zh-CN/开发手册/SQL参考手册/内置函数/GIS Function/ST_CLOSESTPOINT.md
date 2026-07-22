```ebnf
st_closestpoint = ST_CLOSESTPOINT "(" geometry1 "," geometry2 ")".
```

ST_CLOSESTPOINT函数根据输入的geometry1和geometry2，返回geometry1上最接近geometry2的2D点，这个点不一定是输入几何图形的端点。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据，遵循如下规则：

* geometry1和geometry2的空间参考系标识号（SRID）必须相等，否则报错。

本函数遵守如下规则：

* 当输入的参数存在NULL时，函数返回NULL。
* 仅计算2D结果，若输入参数中存在Z坐标，函数将直接忽略Z坐标进行计算。

示例（HEAP表）

```sql
select ST_AsText(ST_ClosestPoint(ST_GeomFromText('POLYGON ((7.000 7.000, 6.000 6.000, 5.444 6.169, 7.000 7.000)) '),ST_GeomFromText('POLYGON((6.400 6.600, 6.231 7.156, 63.24 6.983,  6.400 6.600))')),6) res from dual;

RES
---------------------------------------------------------------- 
POINT (7.000000 7.000000)

select ST_AsText(ST_ClosestPoint(ST_Point(-72.1235, 42.3521, 4326), ST_GeomFromText('polygon((-72.1260 42.45, -72.123 42.1546, -72.1244 42.3527, -72.1260 42.45))',4326)), 6) res from dual;

RES
---------------------------------------------------------------- 
POINT (-72.123500 42.352100)
```
