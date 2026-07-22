```ebnf
st_dump = ST_DUMP "(" geometry ")".
```

The ST_DUMP function is used to return all atomic types (Point, LineString, Polygon) of the input Geometry object and the access paths.

This function returns a collection of geometry_dump types, where each geometry_dump contains:

- geom attribute: type is ST_Geometry, representing the atomic geometry in the input geometry.
- path attribute: a collection of integer type, representing the access path from the input geometry to the atomic geometry.

This function adheres to the following rules:

- If the input parameter contains NULL, the function returns NULL.
- If the input Geometry is atomic, the returned collection contains only one element, where geom is the original input geometry, and path is an empty collection.
- If the input Geometry is of collection type, the returned collection may contain multiple elements, where each element's path is a non-empty integer collection.

**geometry**

[General expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data.

**geometry_dump_set**

The return value of ST_Dump is of geometry_dump_set type. The geometry_dump_set type is a collection type, declared as follows:
```sql
CREATE OR REPLACE TYPE GEOMETRY_DUMP_SET AS TABLE OF GEOMETRY_DUMP;
/
```

path is a collection type with integer members, declared as follows:
```sql
CREATE OR REPLACE TYPE GEOMETRY_PATH AS TABLE OF INTEGER;
/
```

The members of geometry_dump_set are of geometry_dump type, which is an object type declared as follows:
```sql
CREATE OR REPLACE TYPE GEOMETRY_DUMP AS OBJECT (
path GEOMETRY_PATH,
geom ST_GEOMETRY
);
/
```

***Example*** for Heap tables

```sql
select st_astext(t2.geom, 0) from (select st_dump(st_GeomFromText('point(0 0)')) dump from dual) t1, table(t1.dump) t2;

ST_ASTEXT(T2.GEOM,0)
----------------------------------------------------------------
POINT (0 0)

select st_astext(t2.geom, 0) from (select st_dump(st_GeomFromText('multipoint(0 0, 1 1)')) dump from dual) t1, table(t1.dump) t2;

ST_ASTEXT(T2.GEOM,0)
----------------------------------------------------------------
POINT (0 0)
POINT (1 1)

2 rows fetched.

select st_astext(t2.geom, 0) from (select st_dump(st_GeomFromText('geometrycollection(multipoint(0 0, 1 1), geometrycollection(point(2 2), linestring(3 3, 4 4), multipoint(5 5, 6 6)))')) dump from dual) t1, table(t1.dump) t2;

ST_ASTEXT(T2.GEOM,0)
----------------------------------------------------------------
POINT (0 0)
POINT (1 1)
POINT (2 2)
LINESTRING (3 3, 4 4)
POINT (5 5)
POINT (6 6)

-- Create a function to print the path
create or replace function print_path(path MDSYS.GEOMETRY_PATH) return varchar is 
    pathStr varchar(32000);
    i int;
begin
    if path is null then
        return null;
end if;
    
    pathStr := '{';
for i in 1..path.count loop
        if i > 1 then
            pathStr := pathStr || ',';
end if;    
        pathStr := pathStr || path(i);
end loop;
    pathStr := pathStr || '}';
return pathStr;
end;
/

select print_path(t2.path) path, st_astext(t2.geom, 0) geom from (select st_dump(st_GeomFromText('point(0 0)')) dump from dual) t1, table(t1.dump) t2;

PATH                                                             GEOM                                                   
---------------------------------------------------------------- ----------------------------------------------------------------
{}                                                               POINT (0 0)                                            

select print_path(t2.path) path, st_astext(t2.geom, 0) geom from (select st_dump(st_GeomFromText('multipoint(0 0, 1 1)')) dump from dual) t1, table(t1.dump) t2;

PATH                                                             GEOM                                                   
---------------------------------------------------------------- ----------------------------------------------------------------
{1}                                                              POINT (0 0)                                            
{2}                                                              POINT (1 1)                                            

select print_path(t2.path) path, st_astext(t2.geom, 0) geom from (select st_dump(st_GeomFromText('geometrycollection(multipoint(0 0, 1 1), geometrycollection(point(2 2), linestring(3 3, 4 4), multipoint(5 5, 6 6)))')) dump from dual) t1, table(t1.dump) t2;

PATH                                                             GEOM                                                   
---------------------------------------------------------------- ----------------------------------------------------------------
{1,1}                                                            POINT (0 0)                                            
{1,2}                                                            POINT (1 1)                                            
{2,1}                                                            POINT (2 2)                                            
{2,2}                                                            LINESTRING (3 3, 4 4)                                  
{2,3,1}                                                          POINT (5 5)                                            
{2,3,2}                                                          POINT (6 6)                                            
```
