CREATE_PGR_CREATETOPOLOGY is a built-in stored procedure provided by YashanDB, which is used to automatically create other stored procedures for processing GIS data.

### CREATE_PGR_CREATETOPOLOGY

```plsql
CREATE_PGR_CREATETOPOLOGY(); 
```

This procedure is used to create the procedure `PGR_CREATETOPOLOGY`, which takes no input parameters.

***Example*** for Heap tables

```sql
CALL CREATE_PGR_CREATETOPOLOGY();
```

### PGR_CREATETOPOLOGY

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

This procedure is used to create a topological structure for network data. It automatically identifies nodes (vertices) and edges by analyzing geometric columns (usually linestrings) and establishes network topological relationships. 

Execution of this stored procedure requires the user to hold the EXECUTE permission on the stored procedure itself. In addition, the execution of this stored procedure may involve operations such as creating a new table, creating indexes, querying/updating data in existing network data tables, creating indexes on existing network data tables, updating data in existing tables named `{edge_table}_vertices_pgr`, or directly deleting all data rows from existing tables named `{edge_table}_vertices_pgr`. The user must have the corresponding permissions to perform these operations.

>**Note**:
>
> When executing this procedure via *yasql* to connect to the database, it is recommended to first execute the `set serveroutput on` command to enable the output of buffer information, allowing you to promptly obtain the procedure's execution results. A return value of `OK` indicates successful execution.
>
> If using a driver to connect to the database and execute this procedure, you can determine whether execution was successful by checking whether the `{edge_table}_vertices_pgr` table was generated or updated. 
>
> An error will be thrown if any input parameter is NULL. Except for the rows_where parameter, no other parameters can accept subqueries or aggregate functions as inputs. DDL operations cannot be rolled back—even if the execution fails, the topology table will still be created. 

|Parameter |Description |
| :-------- | :----------------------------------------------------------- |
| edge_table   | Name of the network data table. Cannot be NULL. Format: [schema.]table_name, case-insensitive. Omitting the schema indicates the current user's table. <br/> Must specify an existing real table name; cannot be a synonym, and the target table cannot be a temporary table or materialized view. |
| tolerance   | Tolerance value for point capture. Cannot be NULL and cannot use aggregate functions. <br/> Points within the tolerance range are considered the same point. |
| the_geom   | Column name of the Geometry data column in the network data table, cannot be NULL, and is case-insensitive. <br/> When the actual column name in the table is `the_geom` (case-insensitive), this parameter can be omitted. |
| id   | Column name of the primary key column in the network data table, cannot be NULL, and is case-insensitive. <br/>The specified column must be of type INTEGER, SMALLINT, or BIGINT. <br/>When the actual column name in the table is `id` (case-insensitive), this parameter can be omitted.  |
| source   | Column name of the source node column in the network data table, cannot be NULL, and is case-insensitive. <br/>The specified column must be of type INTEGER, SMALLINT, or BIGINT. <br/> When the actual column name in the table is `source` (case-insensitive), this parameter can be omitted. |
| target   | Column name of the target node column in the network data table, cannot be NULL, and is case-insensitive. <br/> The specified column must be of type INTEGER, SMALLINT, or BIGINT. <br/> When the actual column name in the table is `target` (case-insensitive), this parameter can be omitted.  |
| rows_where   | Specifies the filtering condition for the network data to be processed (i.e., the condition used to query data from the network data table), cannot be NULL. Default value is `TRUE`, indicating that only rows where the `source` and `target` columns are NULL are processed. <br/> If you need to customize the condition, the syntax and behavior are similar to the [where_clause](../../SQL Reference Manual/General SQL Syntax/condition) in SELECT statement, and subqueries can also be used directly. |
| clean   | Whether to clear existing vertex information, cannot be NULL. Default value is FALSE. <br/> If set to `TRUE` and the corresponding vertex information table `{edge_table}_vertices_pgr` already exists, the `{edge_table}_vertices_pgr` table will be DROP TABLE IF EXISTS.  |

The following are the results and effects upon successful execution of this procedure:

*   Create a new table in the same schema as the network data table `edge_table` to record the identified vertex information, named `{edge_table}_vertices_pgr`, and create indexes on the `id` and `the_geom` columns of the vertex information table. The table definition is as follows: 

    | Column Name | Data Type |Description |
    | ---------- | ------ | ------ |
    | id      | BIGINT  | Vertex identifier, subject to a uniqueness constraint. <br/>This procedure will insert data into this column based on the analysis results.   |
    | cnt     | INTEGER | Records the number of times this vertex is referenced by the network data table `edge_table`.  |
    | chk     | INTEGER | Indicates whether the vertex data has issues.  |
    | ein     | INTEGER | Records the number of times this vertex is referenced as an input vertex by the network data table `edge_table`. |
    | eout    | INTEGER | Records the number of times this vertex is referenced as an output vertex by the network data table `edge_table`. |
    | the_geom  | Geometry   | The vertex's coordinate information, of type Geometry(Point, srid), with the same SRID as the geometry column in the network data table `edge_table`. <br/> This procedure will insert data into this column based on the analysis results.      |

    If the vertex information table already exists, the data will be updated directly.

*   Update the existing network data table `edge_table`:
    
    *   Populate the source and target columns with values from the `id` column of the vertex information table.

    *   If the `id`, `the_geom`, `source`, and `target` columns in the network data table `edge_table` do not have indexes, indexes will be created for them.

***Example*** for Heap tables

```sql
-- Create a network information table named roads
DROP TABLE IF EXISTS roads;
CREATE TABLE roads(id int, name varchar(10), the_geom geometry, source int, target int);
insert into roads values(1, 'Road A', st_geomfromtext('LINESTRING(0 0, 2 2)'), NULL, NULL);
insert into roads values(2, 'Road B', st_geomfromtext('LINESTRING(2 2, 4 0)'), NULL, NULL);
insert into roads values(3, 'Road C', st_geomfromtext('LINESTRING(2 2, 0 4)'), NULL, NULL);
COMMIT;
SQL> select id, name, source, target, st_astext(the_geom, 0) from roads;

          ID NAME                SOURCE       TARGET ST_ASTEXT(THE_GEOM,0)
------------ ------------- ------------ ------------ ----------------------------------------------------------------
           1 Road A                                  LINESTRING (0 0, 2 2)
           2 Road B                                  LINESTRING (2 2, 4 0)
           3 Road C                                  LINESTRING (2 2, 0 4)

-- Create the PGR_CREATETOPOLOGY procedure
CALL create_pgr_createTopology();

-- Enable printing the cache information 
set serveroutput on

-- Call the PGR_CREATETOPOLOGY procedure to generate topology information based on the `roads` table
call pgr_createTopology('roads', 0.001);
OK vertex information table for table sales.roads is: sales.roads_vertices_pgr

SQL> select id, name, source, target, st_astext(the_geom, 0) from roads;

          ID NAME                SOURCE       TARGET ST_ASTEXT(THE_GEOM,0)
------------ ------------- ------------ ------------ ----------------------------------------------------------------
           1 Road A                   1            2 LINESTRING (0 0, 2 2)
           2 Road B                   2            3 LINESTRING (2 2, 4 0)
           3 Road C                   2            4 LINESTRING (2 2, 0 4)

3 rows fetched.

SQL> select id, cnt, chk, ein, eout, st_astext(the_geom, 0) from roads_vertices_pgr;

                   ID          CNT          CHK          EIN         EOUT ST_ASTEXT(THE_GEOM,0)
--------------------- ------------ ------------ ------------ ------------ -------------------------------------------------------------
                    1                                                     POINT (0 0)
                    2                                                     POINT (2 2)
                    3                                                     POINT (4 0)
                    4                                                     POINT (0 4)

4 rows fetched.

-- Example of Passing NULL as Input Parameter to PGR_CREATETOPOLOGY
call pgr_createTopology('roads', 0.001,clean=>null);
clean cannot be NULL

PL/SQL Succeed.

```
