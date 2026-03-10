```ebnf+diagram
st_makevalid::= ST_MAKEVALID "(" geometry [ "," params ] ")"
```

The ST_MAKEVALID function attempts to create a valid representation for a specified invalid geometry, `geometry`, without losing any vertices from the input, and returns the corresponding valid geometry.

This function adheres to the following rules:

* The input geometry can include points, multipoints, line strings, multi-line strings, polygons, multi-polygons, and collections of any combination of these.
* In cases of partial or complete dimension collapse, the function may return a collection of geometries that can be of lower or equal dimensions, or a lower-dimensional geometry.
* In cases of self-intersection, a single polygon may turn into multiple geometries.

**geometry**

[General Expression](../../General SQL Syntax/expr), which must have a value of a valid ST_GEOMETRY type.

**params**

Used to specify the method for constructing the valid geometry, defined in a `key=value` format, with multiple key-value pairs separated by spaces, formatted as: `method=linework|struct keepcollapsed=true|false`.
* method: Sets the algorithm for constructing the valid geometry, supporting linework and structure algorithms, with a default value of linework.
  * linework: The original algorithm that first extracts and connects all lines, then constructs a valid geometry based on the linework.
  * structure: Distinguishes between inner and outer rings by merging the outer ring to create new geometries, followed by performing a difference operation on all inner rings.
* keepcollapsed: Specifies whether to remove the lowest-dimensional geometric components from the collection, valid only for the structure algorithm.
  * When set to true, geometric components collapsed to the lowest dimension in the collection will not be removed.
  * When set to false, geometric components collapsed to the lowest dimension in the collection will be removed, such as one-dimensional line strings (linestring).

***Example*** for Heap tables

```sql
SELECT ST_AsText(ST_MakeValid(st_geomfromtext('POINT(1 2)')), 0) FROM dual;

ST_ASTEXT(ST_MAKEVALID(ST_GEOMFROMTEXT('POINT(12)')),0)          
---------------------------------------------------------------- 
POINT (1 2)                                                     

SELECT ST_AsText(ST_MakeValid(st_geomfromtext('LINESTRING(0 0, 0 0)'),'method=linework'), 0) FROM dual;

ST_ASTEXT(ST_MAKEVALID(ST_GEOMFROMTEXT('LINESTRING(00,00)'),'MET 
---------------------------------------------------------------- 
POINT (0 0)                                                 

SELECT ST_AsText(ST_MakeValid(st_geomfromtext('POINT(1 0)'), 'method=structure keepcollapsed=true'), 0) FROM dual;

ST_ASTEXT(ST_MAKEVALID(ST_GEOMFROMTEXT('POINT(10)'),'METHOD=STRU 
---------------------------------------------------------------- 
POINT (1 0)
```
