```ebnf+diagram
st_dumppoints::= ST_DUMPPOINTS "(" geometry ")"
```

The ST_DUMP function is used to return all coordinates (vertices) data of the input Geometry object.

This function returns a collection of geometry_dump types, where each geometry_dump contains:

- geom attribute: type is ST_Geometry, representing the POINT coordinates of the input GEOMETRY.
- path attribute: a collection of integer type, representing the coordinate positions in the elements of the provided geometry GEOMETRY, with the index starting from 1. For example, for a LINESTRING geometry, the path is {i}, where i represents the coordinates in this LINESTRING geometry; for a POLYGON geometry, the path is {i,j}, where i is the ring number (1 denotes the outer ring, and inner rings are numbered in increasing order) and j is the coordinate position in this ring.

**geometry**

[General expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

Currently supported input subtypes of ST_GEOMETRY include:

- POINT
- LINESTRING
- POLYGON
- MULTIPOINT
- MULTILINESTRING
- MULTIPOLYGON
- GEOMETRYCOLLECTION

If NULL is input, an error message will be returned.

***Example*** for Heap tables

```sql
-- Create a function to print the path
CREATE OR replace FUNCTION print_path(path MDSYS.GEOMETRY_PATH) RETURN VARCHAR IS 
    pathStr VARCHAR(32000);
    i int;
BEGIN
    IF path IS null THEN
        RETURN null;
END IF;
    
    pathStr := '{';
FOR i IN 1..path.count LOOP
        IF i > 1 THEN
            pathStr := pathStr || ',';
END IF;    
        pathStr := pathStr || path(i);
END LOOP;
    pathStr := pathStr || '}';
RETURN pathStr;
END;
/

SELECT t3.*  , ST_AsText(t2.geom, 0) AS POINT_WKT  FROM (SELECT ST_DUMPPOINTS(
   st_geomfromtext('POLYGON Z(
       (0 0 0, 40 0 10, 40 40 20, 0 40 10, 0 0 0),
       (5 5 5, 35 5 12, 35 35 18, 5 35 12, 5 5 5),
       (10 10 8, 30 10 14, 30 30 16, 10 30 14, 10 10 8),
       (15 15 10, 25 15 13, 25 25 15, 15 25 13, 15 15 10)
   )')
   ) DUMP FROM dual ) t1, TABLE(t1.DUMP) t2, TABLE(t2.path) t3;

COLUMN_VALUE POINT_WKT                                                        
------------ ---------------------------------------------------------------- 
           1 POINT Z (0 0 0)                                                 
           1 POINT Z (0 0 0)                                                 
           1 POINT Z (40 0 10)                                               
           2 POINT Z (40 0 10)                                               
           1 POINT Z (40 40 20)                                              
           3 POINT Z (40 40 20)                                              
           1 POINT Z (0 40 10)                                               
           4 POINT Z (0 40 10)                                               
           1 POINT Z (0 0 0)                                                 
           5 POINT Z (0 0 0)                                                 
           2 POINT Z (5 5 5)                                                 
           1 POINT Z (5 5 5)                                                 
           2 POINT Z (35 5 12)                                               
           2 POINT Z (35 5 12)                                               
           2 POINT Z (35 35 18)                                              
           3 POINT Z (35 35 18)                                              
           2 POINT Z (5 35 12)                                               
           4 POINT Z (5 35 12)                                               
           2 POINT Z (5 5 5)                                                 
           5 POINT Z (5 5 5)                                                 
           3 POINT Z (10 10 8)                                               
           1 POINT Z (10 10 8)                                               
           3 POINT Z (30 10 14)                                              
           2 POINT Z (30 10 14)                                              
           3 POINT Z (30 30 16)                                              
           3 POINT Z (30 30 16)                                              
           3 POINT Z (10 30 14)                                              
           4 POINT Z (10 30 14)                                              
           3 POINT Z (10 10 8)                                               
           5 POINT Z (10 10 8)                                               
           4 POINT Z (15 15 10)                                              
           1 POINT Z (15 15 10)                                              
           4 POINT Z (25 15 13)                                              
           2 POINT Z (25 15 13)                                              
           4 POINT Z (25 25 15)                                              
           3 POINT Z (25 25 15)                                              
           4 POINT Z (15 25 13)                                              
           4 POINT Z (15 25 13)                                              
           4 POINT Z (15 15 10)                                              
           5 POINT Z (15 15 10)                                              

SELECT print_path(t2.path) r1, ST_AsText(t2.geom, 0) r2 FROM (SELECT ST_DUMPPOINTS(
   st_geomfromtext('POLYGON Z(
       (0 0 0, 40 0 10, 40 40 20, 0 40 10, 0 0 0),
       (5 5 5, 35 5 12, 35 35 18, 5 35 12, 5 5 5),
       (10 10 8, 30 10 14, 30 30 16, 10 30 14, 10 10 8),
       (15 15 10, 25 15 13, 25 25 15, 15 25 13, 15 15 10)
   )')
   ) DUMP FROM dual ) t1, TABLE(t1.DUMP) t2;

R1                                                               R2                                                               
---------------------------------------------------------------- ---------------------------------------------------------------- 
{1,1}                                                            POINT Z (0 0 0)                                                 
{1,2}                                                            POINT Z (40 0 10)                                               
{1,3}                                                            POINT Z (40 40 20)                                              
{1,4}                                                            POINT Z (0 40 10)                                               
{1,5}                                                            POINT Z (0 0 0)                                                 
{2,1}                                                            POINT Z (5 5 5)                                                 
{2,2}                                                            POINT Z (35 5 12)                                               
{2,3}                                                            POINT Z (35 35 18)                                              
{2,4}                                                            POINT Z (5 35 12)                                               
{2,5}                                                            POINT Z (5 5 5)                                                 
{3,1}                                                            POINT Z (10 10 8)                                               
{3,2}                                                            POINT Z (30 10 14)                                              
{3,3}                                                            POINT Z (30 30 16)                                              
{3,4}                                                            POINT Z (10 30 14)                                              
{3,5}                                                            POINT Z (10 10 8)                                               
{4,1}                                                            POINT Z (15 15 10)                                              
{4,2}                                                            POINT Z (25 15 13)                                              
{4,3}                                                            POINT Z (25 25 15)                                              
{4,4}                                                            POINT Z (15 25 13)                                              
{4,5}                                                            POINT Z (15 15 10)                                              

SELECT print_path(t2.path) r1, ST_AsText(t2.geom, 0) r2 FROM (SELECT ST_DUMPPOINTS(
   st_GeomFromText('GEOMETRYCOLLECTION (
             POINT ( 0 1 ),
             MULTIPOLYGON (
                     (( 0 5, 0 8, 4 8, 4 5, 0 5 ),
                      ( 1 6, 3 6, 2 7, 1 6 )),
                     (( 5 4, 5 8, 6 7, 5 4 ))
             )
           )')
   ) DUMP FROM dual ) t1, TABLE(t1.DUMP) t2;

R1                                                               R2                                                               
---------------------------------------------------------------- ---------------------------------------------------------------- 
{1,1}                                                            POINT (0 1)                                                     
{2,1,1,1}                                                        POINT (0 5)                                                     
{2,1,1,2}                                                        POINT (0 8)                                                     
{2,1,1,3}                                                        POINT (4 8)                                                     
{2,1,1,4}                                                        POINT (4 5)                                                     
{2,1,1,5}                                                        POINT (0 5)                                                     
{2,1,2,1}                                                        POINT (1 6)                                                     
{2,1,2,2}                                                        POINT (3 6)                                                     
{2,1,2,3}                                                        POINT (2 7)                                                     
{2,1,2,4}                                                        POINT (1 6)                                                     
{2,2,1,1}                                                        POINT (5 4)                                                     
{2,2,1,2}                                                        POINT (5 8)                                                     
{2,2,1,3}                                                        POINT (6 7)                                                     
{2,2,1,4}                                                        POINT (5 4)                                                     

SELECT t3.*  , ST_AsText(t2.geom, 0) AS POINT_WKT  FROM (SELECT ST_DUMPPOINTS(
   st_GeomFromText('GEOMETRYCOLLECTION (
             POINT ( 0 1 ),
             MULTIPOLYGON (
                     (( 0 5, 0 8, 4 8, 4 5, 0 5 ),
                      ( 1 6, 3 6, 2 7, 1 6 )),
                     (( 5 4, 5 8, 6 7, 5 4 ))
             )
           )')
   ) DUMP FROM dual ) t1, TABLE(t1.DUMP) t2, TABLE(t2.path) t3;

COLUMN_VALUE POINT_WKT                                                        
------------ ---------------------------------------------------------------- 
           1 POINT (0 1)                                                     
           1 POINT (0 1)                                                     
           2 POINT (0 5)                                                     
           1 POINT (0 5)                                                     
           1 POINT (0 5)                                                     
           1 POINT (0 5)                                                     
           2 POINT (0 8)                                                     
           1 POINT (0 8)                                                     
           1 POINT (0 8)                                                     
           2 POINT (0 8)                                                     
           2 POINT (4 8)                                                     
           1 POINT (4 8)                                                     
           1 POINT (4 8)                                                     
           3 POINT (4 8)                                                     
           2 POINT (4 5)                                                     
           1 POINT (4 5)                                                     
           1 POINT (4 5)                                                     
           4 POINT (4 5)                                                     
           2 POINT (0 5)                                                     
           1 POINT (0 5)                                                     
           1 POINT (0 5)                                                     
           5 POINT (0 5)                                                     
           2 POINT (1 6)                                                     
           1 POINT (1 6)                                                     
           2 POINT (1 6)                                                     
           1 POINT (1 6)                                                     
           2 POINT (3 6)                                                     
           1 POINT (3 6)                                                     
           2 POINT (3 6)                                                     
           2 POINT (3 6)                                                     
           2 POINT (2 7)                                                     
           1 POINT (2 7)                                                     
           2 POINT (2 7)                                                     
           3 POINT (2 7)                                                     
           2 POINT (1 6)                                                     
           1 POINT (1 6)                                                     
           2 POINT (1 6)                                                     
           4 POINT (1 6)                                                     
           2 POINT (5 4)                                                     
           2 POINT (5 4)                                                     
           1 POINT (5 4)                                                     
           1 POINT (5 4)                                                     
           2 POINT (5 8)                                                     
           2 POINT (5 8)                                                     
           1 POINT (5 8)                                                     
           2 POINT (5 8)                                                     
           2 POINT (6 7)                                                     
           2 POINT (6 7)                                                     
           1 POINT (6 7)                                                     
           3 POINT (6 7)                                                     
           2 POINT (5 4)                                                     
           2 POINT (5 4)                                                     
           1 POINT (5 4)                                                     
           4 POINT (5 4)                                                     

SELECT t3.*  , ST_AsText(t2.geom, 0) AS POINT_WKT  FROM (SELECT ST_DUMPPOINTS(
 st_GeomFromText(null)
 ) DUMP FROM dual ) t1, TABLE(t1.DUMP) t2, TABLE(t2.path) t3;

COLUMN_VALUE POINT_WKT                                                        
------------ ---------------------------------------------------------------- 


SELECT print_path(t2.path) r1, ST_AsText(t2.geom, 0) r2 FROM (SELECT ST_DUMPPOINTS(
 st_GeomFromText(null)
 ) DUMP FROM dual ) t1, TABLE(t1.DUMP) t2;

R1                                                               R2                                                               
---------------------------------------------------------------- ---------------------------------------------------------------- 

```
