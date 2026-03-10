```ebnf+diagram
pgr_dijkstra::= PGR_DIJKSTRA "(" EdgesSQL ","(startVid|startVids) "," (endVid|endVids) ["," directed ]")"
```
```ebnf+diagram
pgr_dijkstra::= PGR_DIJKSTRA "(" EdgesSQL "," CombinationsSQL ["," directed ]")"
```

PGR_DIJKSTRA函数基于Dijkstra算法计算图中从起始点到终点的最短路径，并返回包含以下列的表：SEQ（路径序号）、PATH_SEQ（路径编号）、START_VID（起始点ID）、END_VID（终点ID）、NODE（节点标识符）、EDGE（边标识符）、COST（边代价）、AGG_COST（总开销）。

本函数作为表函数使用，返回包含以下列的表：

| 列名 | 数据类型 | 描述 |
| ---------- | ------ | ------ |
| SEQ      | INTEGER  | 路径的序号，是从1开始的顺序值   |
| PATH_SEQ      | INTEGER  | 路径中的相对位置<br/>值为1表示路径的开始 |
| START_VID     | BIGINT | 起始点的标识ID，仅在多个起始点startVids的计算场景中存在该列  |
| END_VID     | BIGINT | 终点的标识ID，仅在多个终点endVids的计算场景中存在该列  |
| NODE     | BIGINT | 从start_vid到end_vid路径中的节点标识符 |
| EDGE     | BIGINT | 在路径序列中，从一个节点到下一个节点所用的边的标识符。<br/>值为-1表示路径的最后一个节点 |
| COST    | DOUBLE | 从使用边的节点遍历到路径序列中的下一个节点的成本 |
| AGG_COST  | DOUBLE   | 从start_vid到node的总开销 |



*   本函数支持以下计算场景：

    *   单点对单点：计算单个起始点startVid到单个终点endVid的最短路径。

    *   单点对多点：计算单个起始点startVid到多个终点endVids的最短路径。

    *   多点对单点：计算多个起始点startVids到单个终点endVid的最短路径。

    *   多点对多点：计算多个起始点startVids到多个终点endVids的最短路径。

*   本函数可以处理无向图和有向图，且允许用户指定边的权重。



**EdgesSQL**

查询图形数据的SQL语句，其值须为VARCHAR类型以及能够转换成VARCHAR的类型。

*   EdgeSQL执行后所有投影列总大小不能超过63K。

*   EdgesSQL需获取以下信息：

    | 列名 | 支持的数据类型 | 描述 |
    | ------------ | ----------------- | ------------------------- |
    | id           | TINYINT、SMALLINT、INT或BIGINT | 边的标识符               |
    | source       | TINYINT、SMALLINT、INT或BIGINT | 边的第一个端点顶点的标识符         |
    | target       | TINYINT、SMALLINT、INT或BIGINT | 边的第二个端点顶点的标识符         |
    | cost         | TINYINT、SMALLINT、INT、BIGINT、FLOAT、DOUBLE或NUMBER | 从source到target的权重      |
    | reverse_cost | TINYINT、SMALLINT、INT、BIGINT、FLOAT、DOUBLE或NUMBER | 从target到source的权重，默认值为-1 <br/> 当reverse_cost为负数时表示从target到source不可达 |

*   当EdgesSQL为NULL时，函数返回NUL。





**CombinationsSQL**

查询起始点和终点的SQL语句，其值须为VARCHAR类型以及能够转换成VARCHAR的类型。

*   CombinationsSQL需获取以下信息：

    | 列名 | 支持的数据类型 | 描述 |
    | ------------ | ----------------- | ------------------------- |
    | source | TINYINT、SMALLINT、INT或BIGINT | 起始点的标识符 |
    | target | TINYINT、SMALLINT、INT或BIGINT | 终点的标识符 |

*   当此SQL语句为NULL时，函数返回NULL。

**startVid**

起始点的标识符，其值须为BIGINT类型以及能够转换成BIGINT的类型。

当startVid为NULL时，函数返回NULL。

**startVids**

起始点的标识数组，其值须为由BIGINT类型构成的ARRAY类型。

*   如果数组中存在为NULL的元素，函数将报错。

*   当startVids为NULL时，函数返回NULL。

**endVid**

终点的标识符，其值为BIGINT类型以及能够转换成BIGINT的类型。

当endVid为NULL时，函数返回NULL。

**endVids**

终点的标识数组，其值须为由BIGINT类型构成的ARRAY类型。

*   如果数组中存在为NULL的元素，函数将报错。

*   当endVids为NULL时，函数返回NULL。



**directed**

指定EdgesSQL查询的图是否为有向图，其值为BOOLEAN类型以及能够转换成BOOLEAN的类型。

默认值为TRUE（有向图），若设置为FALSE则表示EdgesSQL查询的图为无向图。




示例（HEAP表）

```sql
DROP TABLE IF EXISTS edges;
-- 创建边表
CREATE TABLE edges (
    id INTEGER PRIMARY KEY,
    source INTEGER,
    target INTEGER,
    cost FLOAT,
    reverse_cost FLOAT,
    geom GEOMETRY(LineString, 4326)
);

-- 插入示例数据
INSERT INTO edges VALUES (1, 1, 2, 1.0, 1.0, ST_GeomFromText('LINESTRING(0 0, 1 0)', 4326));
INSERT INTO edges VALUES (2, 2, 3, 1.5, 1.5, ST_GeomFromText('LINESTRING(1 0, 2 0)', 4326));
INSERT INTO edges VALUES (3, 3, 4, 2.0, 2.0, ST_GeomFromText('LINESTRING(2 0, 3 0)', 4326));
INSERT INTO edges VALUES (4, 1, 5, 1.2, 1.2, ST_GeomFromText('LINESTRING(0 0, 0 1)', 4326));
INSERT INTO edges VALUES (5, 5, 6, 0.8, 0.8, ST_GeomFromText('LINESTRING(0 1, 1 1)', 4326));
INSERT INTO edges VALUES (6, 6, 4, 1.7, 1.7, ST_GeomFromText('LINESTRING(1 1, 3 0)', 4326));
COMMIT;

DROP TABLE IF EXISTS combinations;
-- 创建combination表
CREATE TABLE combinations(source INT, target INT);
INSERT INTO combinations VALUES(1, 2);
INSERT INTO combinations VALUES(1, 2);
INSERT INTO combinations VALUES(1, 3);
INSERT INTO combinations VALUES(1, 4);
INSERT INTO combinations VALUES(1, 5);
INSERT INTO combinations VALUES(2, 2);
INSERT INTO combinations VALUES(2, 3);
INSERT INTO combinations VALUES(2, 4);
INSERT INTO combinations VALUES(2, 5);
COMMIT;

-- 查询pgr_dijkstra表
SELECT * FROM pgr_dijkstra('SELECT id, source, target, cost, reverse_cost FROM edges',1, 2);

         SEQ     PATH_SEQ                  NODE                  EDGE        COST    AGG_COST
------------ ------------ --------------------- --------------------- ----------- -----------
           1            1                     1                     1    1.0E+000           0
           2            2                     2                    -1           0    1.0E+000

SELECT * FROM pgr_dijkstra('SELECT id, source, target, cost, reverse_cost FROM edges',1, 3);

         SEQ     PATH_SEQ                  NODE                  EDGE        COST    AGG_COST
------------ ------------ --------------------- --------------------- ----------- -----------
           1            1                     1                     1    1.0E+000           0
           2            2                     2                     2    1.5E+000    1.0E+000
           3            3                     3                    -1           0    2.5E+000
           
SELECT * FROM pgr_dijkstra('SELECT id, source, target, cost, reverse_cost FROM edges',1, ARRAY(2, 3));

         SEQ     PATH_SEQ               endVid                  NODE                  EDGE        COST    AGG_COST
------------ ------------ --------------------- --------------------- --------------------- ----------- -----------
           1            1                     2                     1                     1    1.0E+000           0
           2            2                     2                     2                    -1           0    1.0E+000
           3            1                     3                     1                     1    1.0E+000           0
           4            2                     3                     2                     2    1.5E+000    1.0E+000
           5            3                     3                     3                    -1           0    2.5E+000

SELECT * FROM pgr_dijkstra('SELECT id, source, target, cost, reverse_cost FROM edges',ARRAY(2, 3), 1);

         SEQ     PATH_SEQ             startVid                  NODE                  EDGE        COST    AGG_COST
------------ ------------ --------------------- --------------------- --------------------- ----------- -----------
           1            1                     2                     2                     1    1.0E+000           0
           2            2                     2                     1                    -1           0    1.0E+000
           3            1                     3                     3                     2    1.5E+000           0
           4            2                     3                     2                     1    1.0E+000    1.5E+000
           5            3                     3                     1                    -1           0    2.5E+000

SELECT * FROM pgr_dijkstra('SELECT id, source, target, cost, reverse_cost FROM edges',ARRAY(2, 3), ARRAY(4, 5));

         SEQ     PATH_SEQ             startVid               endVid                  NODE                  EDGE        COST    AGG_COST
------------ ------------ --------------------- --------------------- --------------------- --------------------- ----------- -----------
           1            1                     2                     4                     2                     2    1.5E+000           0
           2            2                     2                     4                     3                     3    2.0E+000    1.5E+000
           3            3                     2                     4                     4                    -1           0    3.5E+000
           4            1                     2                     5                     2                     1    1.0E+000           0
           5            2                     2                     5                     1                     4    1.2E+000    1.0E+000
           6            3                     2                     5                     5                    -1           0    2.2E+000
           7            1                     3                     4                     3                     3    2.0E+000           0
           8            2                     3                     4                     4                    -1           0    2.0E+000
           9            1                     3                     5                     3                     2    1.5E+000           0
          10            2                     3                     5                     2                     1    1.0E+000    1.5E+000
          11            3                     3                     5                     1                     4    1.2E+000    2.5E+000
          12            4                     3                     5                     5                    -1           0    3.7E+000

SELECT * FROM pgr_dijkstra('SELECT id, source, target, cost, reverse_cost FROM edges', 'select source, target from combinations');

         SEQ     PATH_SEQ             startVid               endVid                  NODE                  EDGE        COST    AGG_COST
------------ ------------ --------------------- --------------------- --------------------- --------------------- ----------- -----------
           1            1                     1                     2                     1                     1    1.0E+000           0
           2            2                     1                     2                     2                    -1           0    1.0E+000
           3            1                     1                     3                     1                     1    1.0E+000           0
           4            2                     1                     3                     2                     2    1.5E+000    1.0E+000
           5            3                     1                     3                     3                    -1           0    2.5E+000
           6            1                     1                     4                     1                     4    1.2E+000           0
           7            2                     1                     4                     5                     5    8.0E-001    1.2E+000
           8            3                     1                     4                     6                     6    1.7E+000    2.0E+000
           9            4                     1                     4                     4                    -1           0    3.7E+000
          10            1                     1                     5                     1                     4    1.2E+000           0
          11            2                     1                     5                     5                    -1           0    1.2E+000
          12            1                     2                     3                     2                     2    1.5E+000           0
          13            2                     2                     3                     3                    -1           0    1.5E+000
          14            1                     2                     4                     2                     2    1.5E+000           0
          15            2                     2                     4                     3                     3    2.0E+000    1.5E+000
          16            3                     2                     4                     4                    -1           0    3.5E+000
          17            1                     2                     5                     2                     1    1.0E+000           0
          18            2                     2                     5                     1                     4    1.2E+000    1.0E+000
          19            3                     2                     5                     5                    -1           0    2.2E+000



-- 本函数不支持绑定参数，报错YAS-07204 no function matches the given name and argument types
SET serveroutput ON;
DROP TABLE IF EXISTS   main_edges_ydbrd3621_84;
CREATE TABLE main_edges_ydbrd3621_84 (
    id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    source INT NOT NULL,
    target INT NOT NULL,
    cost INT NOT NULL,
    reverse_cost INT,
    category_id INT,
    region_id INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
-- 创建分类表
DROP TABLE IF EXISTS   categories_ydbrd3621_84;
CREATE TABLE categories_ydbrd3621_84 (
    id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    priority INT DEFAULT 1,
    is_active INT DEFAULT 1
);
-- 创建区域表
DROP TABLE IF EXISTS   regions_ydbrd3621_84;
CREATE TABLE regions_ydbrd3621_84 (
    id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    population INT,
    area_code VARCHAR(10)
);
-- 插入基础数据
INSERT INTO categories_ydbrd3621_84 (name, priority, is_active) VALUES ('highway', 3, 1);
INSERT INTO categories_ydbrd3621_84 (name, priority, is_active) VALUES ('avenue', 2, 1);
INSERT INTO categories_ydbrd3621_84 (name, priority, is_active) VALUES ('street', 1, 1);
INSERT INTO categories_ydbrd3621_84 (name, priority, is_active) VALUES ('disabled', 0, 0);
INSERT INTO regions_ydbrd3621_84 (name, population, area_code) VALUES ('downtown', 50000, 'DT');
INSERT INTO regions_ydbrd3621_84 (name, population, area_code) VALUES ('suburban', 30000, 'SB');
INSERT INTO regions_ydbrd3621_84 (name, population, area_code) VALUES ('industrial', 20000, 'ID');
INSERT INTO main_edges_ydbrd3621_84 (source, target, cost, reverse_cost, category_id, region_id) VALUES
(1, 2, 1.0, 1.0, 1, 1),
(2, 3, 1.5, 1.5, 1, 1),
(3, 4, 2.0, 2.0, 1, 1),
(1, 5, 3.0, 3.0, 2, 2),
(5, 6, 1.2, 1.2, 2, 2),
(6, 4, 1.8, 1.8, 2, 2),
(2, 7, 2.5, 2.5, 3, 3),
(7, 8, 1.3, 1.3, 3, 3),
(8, 4, 2.2, 2.2, 3, 3);
COMMIT;

DECLARE
    v_edges_sql VARCHAR2(1000) := 'SELECT id, source, target, cost FROM main_edges_ydbrd3621_84 WHERE cost <= :max_cost';
    v_max_cost NUMBER := 3.0;
    v_start_node NUMBER := 1;
    v_distance NUMBER := 4.0;
    v_directed BOOLEAN := TRUE;
    v_result_count NUMBER;
BEGIN
    DBMS_OUTPUT.PUT_LINE('=== pgr_drivingDistance comprehensive scenario bound parameter testing ===');
    -- 构建动态SQL字符串（避免绑定变量在SQL字符串中的问题）
    DECLARE
        v_dynamic_edges_sql VARCHAR2(1000);
    BEGIN
        -- 构建包含具体值的SQL字符串
        v_dynamic_edges_sql := 'SELECT id, source, target, cost FROM main_edges_ydbrd3621_84 WHERE cost <= ' || v_max_cost;
        
        DBMS_OUTPUT.PUT_LINE('Used edge query SQL: ' || v_dynamic_edges_sql);
        DBMS_OUTPUT.PUT_LINE('Starting point: ' || v_start_node || ', Distance Limit: ' || v_distance ||
                           ', Directed: ' || CASE WHEN v_directed THEN 'Yes' ELSE 'No' END);
        
        -- 计算可达节点数量
        EXECUTE IMMEDIATE '
            SELECT COUNT(*) FROM pgr_drivingDistance(:edges_sql, :start_vid, :distance, :directed_flag)'
        INTO v_result_count
        USING v_dynamic_edges_sql, v_start_node, v_distance, v_directed;
        
        DBMS_OUTPUT.PUT_LINE('Comprehensive test for reachable node count:' || v_result_count);
        -- 如果有可达节点，显示详细结果
        IF v_result_count > 0 THEN
            DBMS_OUTPUT.PUT_LINE('Details of reachable nodes:');
            
            DECLARE
                v_sql VARCHAR2(2000);
                v_directed_str VARCHAR2(10);
            BEGIN
                -- 构建执行SQL
                IF v_directed THEN
                    v_directed_str := 'TRUE';
                ELSE
                    v_directed_str := 'FALSE';
                END IF;
                
                v_sql := 'SELECT seq, node, edge, cost, agg_cost FROM pgr_drivingDistance(''' ||
                         v_dynamic_edges_sql || ''', ' || v_start_node || ', ' || v_distance ||
                         ', ' || v_directed_str || ')';
                
                EXECUTE IMMEDIATE '
                    DECLARE
                        CURSOR c_result IS ' || v_sql || ';
                        v_rec c_result%ROWTYPE;
                    BEGIN
                        OPEN c_result;
                        LOOP
                            FETCH c_result INTO v_rec;
                            EXIT WHEN c_result%NOTFOUND;
                            DBMS_OUTPUT.PUT_LINE(''  seq:'' || v_rec.seq || 
                                               '', node:'' || v_rec.node || '', edge:'' || v_rec.edge ||
                                               '', cost:'' || v_rec.cost || '', agg_cost:'' || v_rec.agg_cost);
                        END LOOP;
                        CLOSE c_result;
                    END;';
            END;
        ELSE
            DBMS_OUTPUT.PUT_LINE('No reachable nodes found under the given conditions');
        END IF;
        
    EXCEPTION
        WHEN OTHERS THEN
            DBMS_OUTPUT.PUT_LINE('Comprehensive test execution failed: ' || SQLERRM);
            DBMS_OUTPUT.PUT_LINE('Error code: ' || SQLCODE);
    END;
END;
/

Execution results: YAS-07204 no function matches the given name and argument types


```
