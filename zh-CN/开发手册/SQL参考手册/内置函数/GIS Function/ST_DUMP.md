```ebnf
st_dump = ST_DUMP "(" geometry ")".
```

ST_DUMP函数用于返回输入的Geometry对象的所有原子类型（Point、LineString、Polygon）及访问路径。

本函数返回一组geometry_dump类型的集合，每个geometry_dump包含：

- geom属性：类型为ST_Geometry，表示输入geometry中的原子geometry。
- path属性：integer类型的集合，表示从输入的geometry到该原子geometry的访问路径。

本函数遵守如下规则：

- 当输入的参数存在NULL时，函数返回NULL。
- 当输入的Geometry为原子类型时，返回的集合中只有一个元素，其中geom为原始输入的geometry，path为一个空的集合。
- 当输入的Geometry为集合类型时，返回的集合中可能包含多个元素，其中每个元素的path为一个非空的integer集合。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

**geometry_dump_set**

ST_Dump返回值为geometry_dump_set类型。geometry_dump_set类型是一个集合类型，其声明如下：
```sql
CREATE OR REPLACE TYPE GEOMETRY_DUMP_SET AS TABLE OF GEOMETRY_DUMP;
/
```

path是一个成员为integer类型的集合类型，其声明如下：
```sql
CREATE OR REPLACE TYPE GEOMETRY_PATH AS TABLE OF INTEGER;
/
```

geometry_dump_set的成员为geometry_dump类型，geometry_dump类型为一个对象类型，其声明如下：
```sql
CREATE OR REPLACE TYPE GEOMETRY_DUMP AS OBJECT (
path GEOMETRY_PATH,
geom ST_GEOMETRY
);
/
```

示例（HEAP表）

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
