```ebnf
st_dumppoints = ST_DUMPPOINTS "(" geometry ")".
```

ST_DUMPPOINTS函数用于返回输入的Geometry对象的所有坐标点信息。

本函数返回一组geometry_dump类型的集合，每个geometry_dump包含：

- geom属性：类型为ST_Geometry，表示输入的几何体GEOMETRY的POINT坐标。
- path属性：integer类型的集合，枚举了所提供几何体GEOMETRY元素中的坐标位置，索引从1开始。例如，对于一个LINESTRING几何体，路径为`{i}`，其中i该几何体中的坐标LINESTRING；对于一个POLYGON几何体，路径为`{i,j}`，其中i是环号（1表示外环，内环依次递增），j是该环中的坐标位置。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

当前支持输入的子数据类型为：

- POINT
- LINESTRING
- POLYGON
- MULTIPOINT
- MULTILINESTRING
- MULTIPOLYGON
- GEOMETRYCOLLECTION

当输入的参数为NULL时，返回报错信息。

示例（HEAP表）

```sql
--创建一个打印path的函数
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

select t3.*  , ST_AsText(t2.geom, 0) as POINT_WKT  from (select ST_DUMPPOINTS(
   st_geomfromtext('POLYGON Z(
       (0 0 0, 40 0 10, 40 40 20, 0 40 10, 0 0 0),
       (5 5 5, 35 5 12, 35 35 18, 5 35 12, 5 5 5),
       (10 10 8, 30 10 14, 30 30 16, 10 30 14, 10 10 8),
       (15 15 10, 25 15 13, 25 25 15, 15 25 13, 15 15 10)
   )')
   ) dump from dual ) t1, table(t1.dump) t2, table(t2.path) t3;

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

select print_path(t2.path) r1, ST_AsText(t2.geom, 0) r2 from (select ST_DUMPPOINTS(
   st_geomfromtext('POLYGON Z(
       (0 0 0, 40 0 10, 40 40 20, 0 40 10, 0 0 0),
       (5 5 5, 35 5 12, 35 35 18, 5 35 12, 5 5 5),
       (10 10 8, 30 10 14, 30 30 16, 10 30 14, 10 10 8),
       (15 15 10, 25 15 13, 25 25 15, 15 25 13, 15 15 10)
   )')
   ) dump from dual ) t1, table(t1.dump) t2;

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

select print_path(t2.path) r1, ST_AsText(t2.geom, 0) r2 from (select ST_DUMPPOINTS(
   st_GeomFromText('GEOMETRYCOLLECTION (
             POINT ( 0 1 ),
             MULTIPOLYGON (
                     (( 0 5, 0 8, 4 8, 4 5, 0 5 ),
                      ( 1 6, 3 6, 2 7, 1 6 )),
                     (( 5 4, 5 8, 6 7, 5 4 ))
             )
           )')
   ) dump from dual ) t1, table(t1.dump) t2;

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

select t3.*  , ST_AsText(t2.geom, 0) as POINT_WKT  from (select ST_DUMPPOINTS(
   st_GeomFromText('GEOMETRYCOLLECTION (
             POINT ( 0 1 ),
             MULTIPOLYGON (
                     (( 0 5, 0 8, 4 8, 4 5, 0 5 ),
                      ( 1 6, 3 6, 2 7, 1 6 )),
                     (( 5 4, 5 8, 6 7, 5 4 ))
             )
           )')
   ) dump from dual ) t1, table(t1.dump) t2, table(t2.path) t3;

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

select t3.*  , ST_AsText(t2.geom, 0) as POINT_WKT  from (select ST_DUMPPOINTS(
 st_GeomFromText(null)
 ) dump from dual ) t1, table(t1.dump) t2, table(t2.path) t3;

COLUMN_VALUE POINT_WKT                                                        
------------ ---------------------------------------------------------------- 


select print_path(t2.path) r1, ST_AsText(t2.geom, 0) r2 from (select ST_DUMPPOINTS(
 st_GeomFromText(null)
 ) dump from dual ) t1, table(t1.dump) t2;

R1                                                               R2                                                               
---------------------------------------------------------------- ---------------------------------------------------------------- 

```
