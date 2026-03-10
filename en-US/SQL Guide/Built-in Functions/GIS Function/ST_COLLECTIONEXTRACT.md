```ebnf+diagram
st_collectionextract::= ST_CollectionExtract "(" geometry ["," type ] ")"
```

The ST_COLLECTIONEXTRACT function is used to extract geometries of the same type from a specified geometry collection.

**geometry**

[General Expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), whose value must be valid ST_GEOMETRY type data.

**type**

Specifies the geometry type of the return value. It is optional and can be omitted. The options include 1, 2, and 3, which respectively represent POINT, LINESTRING, and POLYGON.

* If no type is specified, the function returns a multi-geometric object that contains geometries of the highest dimension. Dimension levels are: POLYGON > LINE > POINT.
* If a type is specified, the function returns a multi-geometric object that contains geometries of the specified type. If no element of the specified type exists in geometry, it returns EMPTY.

***Example*** for Heap tables

```sql
SELECT ST_AsText(ST_CollectionExtract(st_geomfromtext('POINT(16 16)'), 1), 0) FROM dual;

ST_ASTEXT(ST_COLLECTIONEXTRACT(ST_GEOMFROMTEXT('POINT(1616)'),1) 
---------------------------------------------------------------- 
POINT (16 16)                                                     

SELECT ST_AsText(ST_CollectionExtract(st_geomfromtext('MULTILINESTRING Z ((0 0 0, 2 0 0), (2.09887763434 3.91 0, 2 2 0))'), 2), 0) FROM dual;

ST_ASTEXT(ST_COLLECTIONEXTRACT(ST_GEOMFROMTEXT('MULTILINESTRINGZ 
---------------------------------------------------------------- 
MULTILINESTRING Z ((0 0 0, 2 0 0), (2 4 0, 2 2 0))                                                

SELECT ST_AsText(ST_CollectionExtract(st_geomfromtext('GEOMETRYCOLLECTION Z (MULTIPOLYGON Z (((0 0 5,10 0 5,10 10 5,0 10 5,0 0 5),(2 2 5,2 5 5,5 5 5,5 2 5,2 2 5))),POINT Z (0 0 5),MULTILINESTRING Z ((0 0 5, 2 0 5),(1 1 5, 2 2 5)))')), 0) FROM dual;

ST_ASTEXT(ST_COLLECTIONEXTRACT(ST_GEOMFROMTEXT('GEOMETRYCOLLECTI 
---------------------------------------------------------------- 
MULTIPOLYGON Z (((0 0 5, 10 0 5, 10 10 5, 0 10 5, 0 0 5), (2 2 5, 2 5 5, 5 5 5, 5 2 5, 2 2 5)))
```
