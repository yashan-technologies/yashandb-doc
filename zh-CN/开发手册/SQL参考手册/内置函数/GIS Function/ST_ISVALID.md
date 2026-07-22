```ebnf
st_isvalid = ST_ISVALID "(" geometry ")".
```

ST_ISVALID函数根据输入的geometry，返回该geometry是否有效，如该geometry是有效的，则返回TRUE，否则返回FALSE。

ST_GEOMETRY类型数据是否有效须遵循OGC SFS规范，按照如下规则判断该数据是否有效：

- POINT：均为有效。
- MULTIPOINT：均为有效。
- LINESTRING：均为有效。
- MULTILINESTRING：均为有效。
- POLYGON：有效的POLYGON类型数据须遵循如下限制，否则视为无效：
  - 各环必须闭合。
  - 内环必须处于外环内部。
  - 各环不能自相交。
  - 环之间不能接触，除非在一个点相切。
- MULTIPOLYGON：其中所有元素均为有效，且元素内部不能相交时，该数据为有效；否则不为有效。
- GEOMETRYCOLLECTION：其中所有元素均为有效时，该数据为有效；否则不为有效。
- 数据中均为EMPTY时，该数据为有效。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

当输入的参数存在NULL时，函数返回NULL，空串作为NULL处理。

仅计算2D结果，若输入参数中存在Z坐标，函数将直接忽略Z坐标进行计算。

示例（HEAP表）

```sql
SELECT ST_IsValid(ST_GeomFromText('LINESTRING(0 0, 1 1)')) res FROM DUAL;

RES
-------------------- 
true

SELECT ST_IsValid(ST_GeomFromText('POLYGON((0 0, 1 1, 1 2, 1 1, 0 0))')) res FROM DUAL;

RES
-------------------- 
false               
```
