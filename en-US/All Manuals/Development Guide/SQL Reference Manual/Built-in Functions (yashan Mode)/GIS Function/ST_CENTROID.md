```ebnf+diagram
st_centroid::= ST_Centroid"(" geometry "," use_spheroid ")"
```

The ST_CENTROID function is used to calculate the centroid point of a specified geometry.

This function adheres to the following rules:

* The function returns the centroid of a polygon, which may not necessarily lie within the polygon.
* The function specifies the geometry SRID as 4326 to indicate latitude and longitude coordinates.
* The latitude and longitude coordinate calculation currently only supports the ellipsoidal method.

**geometry**

[General Expression](../../General SQL Syntax/expr), its value must be a valid ST_GEOMETRY type.

**use_spheroid**

Specifies whether to use the ellipsoidal method for calculation, default is true.

***Example*** for Heap tables

```sql
SELECT ST_AsText(ST_Centroid(st_geomfromtext('MULTIPOINT((-15 -15), (5 5))')), 0) FROM dual;

ST_ASTEXT(ST_CENTROID(ST_GEOMFROMTEXT('MULTIPOINT((-15-15),(55)) 
---------------------------------------------------------------- 
POINT (-5 -5) 

SELECT ST_AsText(ST_Centroid(st_geomfromtext('MULTILINESTRING ZM ((0 0 0 0, 2.09887763434 3.9000000000000009 0 0), (1 1 0 0, 2 2 0 0))', 4326),true), 0) FROM dual;

ST_ASTEXT(ST_CENTROID(ST_GEOMFROMTEXT('MULTILINESTRINGZM((0000,2 
---------------------------------------------------------------- 
POINT (1 2)                                                     
```
