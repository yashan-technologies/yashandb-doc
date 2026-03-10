```ebnf+diagram
st_isclosed::= ST_IsClosed "(" geometry ")"
```

ST_ISCLOSED函数用于判断线的首、尾两个点是否重合，通常用于判断LineString的首尾是否闭合。

本函数遵守如下规则：

- 当输入对象为NULL，返回NULL。
- 当输入对象为空几何对象（如LineString Empty），返回FALSE。
- 其他情况下对于不同的几何对象类型，其行为如下：

|  对象类型| ST_IsClosed函数行为|
|------|------------------|
|Point| 返回true|
|LineString|判断LineString的首尾是否重合|
|Polygon|判断Polygon中的每个环是否首尾闭合，所有环均闭合返回true， 否则返回false|
|Multi及Collection类型|判断集合中每个成员是否闭合，所有成员均闭合返回true，否则返回false|

**geometry**

[通用表达式](../../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

示例（HEAP表）

```sql
SELECT st_isclosed(st_geomfromText('point(0 0)')) isclosed FROM dual;

ISCLOSED
--------------------
true

SELECT st_isclosed(st_geomfromText('linestring(0 0, 1 1, 1 0)')) isclosed FROM dual;

ISCLOSED
--------------------
false

SELECT st_isclosed(st_geomfromText('linestring(0 0, 1 1, 1 0, 0 0)')) isclosed FROM dual;

ISCLOSED
--------------------
true

SELECT st_isclosed(st_geomfromText('polygon((0 0, 1 1, 1 0, 0 0))')) isclosed FROM dual;

ISCLOSED
--------------------
true

SELECT st_isclosed(st_geomfromText('multipoint(0 0, 1 1)')) isclosed FROM dual;

ISCLOSED
--------------------
true

SELECT st_isclosed(st_geomfromText('multilinestring((0 0, 1 1, 1 0, 0 0), (0 0, 1 1, 1 0, 0 0))')) isclosed FROM dual;

ISCLOSED
--------------------
true

SELECT st_isclosed(st_geomfromText('multilinestring((0 0, 1 1, 1 0, 0 0), (0 0, 1 1, 1 0, 0 1))')) isclosed FROM dual;

ISCLOSED
--------------------
false
```
