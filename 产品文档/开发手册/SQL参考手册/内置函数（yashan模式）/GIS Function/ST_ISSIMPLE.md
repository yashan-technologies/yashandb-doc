```ebnf+diagram
st_issimple::= ST_ISSIMPLE "(" geometry ")"
```

ST_ISSIMPLE函数根据输入的geometry，返回该geometry是否简单，如该geometry符合简单定义，则返回TRUE，否则返回FALSE。

ST_GEOMETRY类型数据的简单定义遵循OGC SFS规范，按照如下规则判断该数据是否简单：

- POINT：均为简单。
- MULTIPOINT：所有点唯一时，该数据为简单；否则不为简单。
- LINESTRING：除端点之外的所有点唯一时，该数据为简单；否则不为简单。
- MULTILINESTRING：其中所有元素均为简单，且任意两个元素之间的唯一交集出现在两个元素的边界上时，该数据为简单；否则不为简单。
- POLYGON：由简单且闭环的LINESTRING组成，且该POLYGON数据为有效时，该数据为简单；否则不为简单。有效定义可参考[ST_ISVALID](ST_ISVALID)函数描述。
- MULTIPOLYGON：其中所有元素均为简单时，该数据为简单；否则不为简单。
- GEOMETRYCOLLECTION：其中所有元素均为简单时，该数据为简单；否则不为简单。
- 数据中均为EMPTY时，该数据为简单。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

当输入的参数存在NULL时，函数返回NULL，空串作为NULL处理。


示例（HEAP表）

```sql
SELECT ST_IsSimple(ST_GeomFromText('POLYGON((1 2, 3 4, 5 6, 1 2))')) res FROM DUAL;

RES
-------------------- 
false

SELECT ST_IsSimple(ST_GeomFromText('POLYGON((1 2, 3 3, 5 6, 1 2))')) res FROM DUAL;

RES
-------------------- 
true                
```
