```ebnf
st_isclosed = ST_IsClosed "(" geometry ")".
```

The ST_ISCLOSED function is used to determine whether the starting and ending points of a line coincide, typically used to check whether the ends of a LineString are closed.

This function adheres to the following rules:

- If the input object is NULL, it returns NULL.
- If the input object is an empty geometry object (e.g., LineString Empty), it returns FALSE.
- In all other cases, the behavior varies based on the different geometric object types as follows:

|Object Type |ST_IsClosed Function Behavior |
|------|------------------|
| Point                  | Returns true                                                                         |
| LineString             | Checks if the starting and ending points of the LineString coincide                 |
| Polygon                | Checks if each ring in the Polygon is closed; returns true if all rings are closed; otherwise returns false |
| Multi and Collection Types | Checks if each member in the collection is closed; returns true if all members are closed; otherwise returns false |

**geometry**

[General Expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

***Example*** for Heap tables

```sql
select st_isclosed(st_geomfromText('point(0 0)')) isclosed from dual;

ISCLOSED
--------------------
true

select st_isclosed(st_geomfromText('linestring(0 0, 1 1, 1 0)')) isclosed from dual;

ISCLOSED
--------------------
false

select st_isclosed(st_geomfromText('linestring(0 0, 1 1, 1 0, 0 0)')) isclosed from dual;

ISCLOSED
--------------------
true

select st_isclosed(st_geomfromText('polygon((0 0, 1 1, 1 0, 0 0))')) isclosed from dual;

ISCLOSED
--------------------
true

select st_isclosed(st_geomfromText('multipoint(0 0, 1 1)')) isclosed from dual;

ISCLOSED
--------------------
true

select st_isclosed(st_geomfromText('multilinestring((0 0, 1 1, 1 0, 0 0), (0 0, 1 1, 1 0, 0 0))')) isclosed from dual;

ISCLOSED
--------------------
true

select st_isclosed(st_geomfromText('multilinestring((0 0, 1 1, 1 0, 0 0), (0 0, 1 1, 1 0, 0 1))')) isclosed from dual;

ISCLOSED
--------------------
false
```
