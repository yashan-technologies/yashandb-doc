CREATE_PGR_CREATETOPOLOGY包提供了内置的存储过程，用于处理GIS数据。

### create\_pgr\_createTopology

```plsql
create_pgr_createTopology(); 
```

该存储过程用于创建内置存储过程pgr_createTopology，无入参。

示例（HEAP表）

```sql
CALL create_pgr_createTopology();
```

### pgr\_createTopology

```plsql
PGR_CREATETOPOLOGY (
    edge_table IN VARCHAR,
    tolerance IN DOUBLE,
    the_geom IN VARCHAR DEFAULT 'the_geom',
    id IN VARCHAR DEFAULT 'id',
    source IN VARCHAR DEFAULT 'source',
    target IN VARCHAR DEFAULT 'target',
    rows_where IN VARCHAR DEFAULT 'true',
    clean IN BOOLEAN DEFAULT FALSE);
```

该存储过程用于为网络数据创建拓扑结构，通过分析几何列（通常是线串）来自动识别节点（顶点）和边，并建立网络拓扑关系。


执行本存储过程要求用户具备该存储过程的EXECUTE权限。另外，本存储过程还可能涉及创建新表、创建索引、查询/更新现有网络数据表的数据、为现有网络数据表创建索引、更新已有同名{edge_table}_vertices_pgr表的数据或直接删除已有同名{edge_table}_vertices_pgr表的所有数据行等操作，要求用户具备相应的权限。

>**Note**:
>
> 
> 在使用yasql时连接数据库执行本存储过程时，建议先执行set serveroutput on命令开启打印缓存区信息的功能，方便及时获取函数执行结果，返回`OK`表示执行成功。
>
> 若使用驱动连接数据库执行本存储过程，可通过检查是否生成或更新{edge_table}_vertices_pgr表进而判断是否执行成功。
>
> 所有参数入参NULL都会报错，除了rows_where外，其他参数没法入参子查询和聚合函数。对于DDL没法回滚，哪怕执行失败了也会创建拓扑表。

|  参数| 描述|
| :-------- | :----------------------------------------------------------- |
| edge_table   | 网络数据表的名称，不能为空，格式为[schema.]table_name，大小写不敏感，省略schema时表示当前用户的表。<br/>需指定已存在的真实表名，不能为同义词，且指定的目标表不能为临时表或物化视图。 |
| tolerance   | 捕捉点的容差值，不能为空且不能使用聚集函数。<br/>在容差范围内的点将被视作同一个点。 |
| the_geom   | 网络数据表中几何数据列的列名，不能为空，大小写不敏感。<br/>当表中的实际列名为`the_geom`（大小写不敏感）时，可省略该参数。 |
| id   | 网络数据表的主键列的列名，不能为空，大小写不敏感。<br/>指定的列必须是INTEGER、SMALLINT或BIGINT类型。<br/>当表中的实际列名为`id`（大小写不敏感）时，可省略该参数。 |
| source   | 网络数据表中记录起始点的列名，不能为空，大小写不敏感。<br/>指定的列必须是INTEGER、SMALLINT或BIGINT类型。<br/>当表中的实际列名为`source`（大小写不敏感）时，可省略该参数。 |
| target   | 网络数据表中记录终点的列名，不能为空，大小写不敏感。<br/> 指定的列必须是INTEGER、SMALLINT或BIGINT类型。<br/> 当表中的实际列名为`target`（大小写不敏感）时，可省略该参数。 |
|  rows_where  | 指定需要进行处理的网络数据过滤条件（即根据该条件从网络数据表中查询数据，不能为空。默认值为TRUE，表示仅处理起始点列和终点列为空的数据行。 <br/> 如需自定义指定，语法及效果与SELECT语句的[WHERE条件子句](../../全部手册/开发手册/SQL参考手册/通用SQL语法/condition)类似，也可以直接使用子查询。 |
| clean   | 是否清理现有的顶点信息，不能为空，默认值为FALSE。 <br/>若设置为TRUE且已存在对应的顶点信息表{edge_table}_vertices_pgr时，会对{edge_table}_vertices_pgr表执行DROP TABLE IF EXISTS操作。 |

执行成功的结果及影响如下：

*   在网络数据表edge_table同一schema下创建一个新表记录所识别到的顶点信息，表名为{edge_table}_vertices_pgr，同时会在顶点信息表的id列和the_geom列上建立索引，表的定义如下：

    | 列名 | 数据类型 | 描述 |
    | ---------- | ------ | ------ |
    | id      | BIGINT  | 顶点的标识符，满足唯一约束<br/>本存储过程会根据分析结果向该列插入数据   |
    | cnt     | INTEGER | 记录该顶点被网络数据表edge_table引用的次数  |
    | chk     | INTEGER | 标识该顶点数据是否存在问题  |
    | ein     | INTEGER | 记录该顶点被网络数据表edge_table引用作为传入顶点的次数 |
    | eout    | INTEGER | 记录该顶点被网络数据表edge_table引用作为输出顶点的次数 |
    | the_geom  | Geometry   | 该顶点的坐标信息，是`Geometry(Point,srid)`类型，其srid与原有网络数据表edge_table中几何列的srid相同<br/>本存储过程会根据分析结果向该列插入数据      |

    若已存在该顶点信息表，则直接更新数据。

*   更新现有网络数据表edge_table：
    
    *   引用顶点信息表中的的id列值填充起始点列和终点列的值。

    *   若网络数据表edge_table的id、the_geom、source、target列不存在索引，将会为其创建索引。

示例（HEAP表）

```sql
-- 创建一个网络信息表roads
DROP TABLE IF EXISTS roads;
CREATE TABLE roads(id INT, name VARCHAR(10), the_geom geometry, source INT, target INT);
INSERT INTO roads VALUES(1, 'Road A', st_geomfromtext('LINESTRING(0 0, 2 2)'), NULL, NULL);
INSERT INTO roads VALUES(2, 'Road B', st_geomfromtext('LINESTRING(2 2, 4 0)'), NULL, NULL);
INSERT INTO roads VALUES(3, 'Road C', st_geomfromtext('LINESTRING(2 2, 0 4)'), NULL, NULL);
COMMIT;
SELECT id, name, source, target, st_astext(the_geom, 0) FROM roads;

          ID NAME                SOURCE       TARGET ST_ASTEXT(THE_GEOM,0)
------------ ------------- ------------ ------------ ----------------------------------------------------------------
           1 Road A                                  LINESTRING (0 0, 2 2)
           2 Road B                                  LINESTRING (2 2, 4 0)
           3 Road C                                  LINESTRING (2 2, 0 4)

-- 创建PGR_CREATETOPOLOGY存储过程
CALL create_pgr_createTopology();

-- 开启打印缓冲区信息
SET serveroutput ON

-- 调用PGR_CREATETOPOLOGY存储过程基于roads表生成拓扑信息
CALL pgr_createTopology('roads', 0.001);
OK vertex information table for table sales.roads is: sales.roads_vertices_pgr

SELECT id, name, source, target, st_astext(the_geom, 0) FROM roads;

          ID NAME                SOURCE       TARGET ST_ASTEXT(THE_GEOM,0)
------------ ------------- ------------ ------------ ----------------------------------------------------------------
           1 Road A                   1            2 LINESTRING (0 0, 2 2)
           2 Road B                   2            3 LINESTRING (2 2, 4 0)
           3 Road C                   2            4 LINESTRING (2 2, 0 4)


SELECT id, cnt, chk, ein, eout, st_astext(the_geom, 0) FROM roads_vertices_pgr;

                   ID          CNT          CHK          EIN         EOUT ST_ASTEXT(THE_GEOM,0)
--------------------- ------------ ------------ ------------ ------------ -------------------------------------------------------------
                    1                                                     POINT (0 0)
                    2                                                     POINT (2 2)
                    3                                                     POINT (4 0)
                    4                                                     POINT (0 4)


-- 调用PGR_CREATETOPOLOGY入参NULL示例
call pgr_createTopology('roads', 0.001,clean=>null);
clean cannot be NULL


```
