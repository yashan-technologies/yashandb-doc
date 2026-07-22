```ebnf
pgr_drivingdistance = PGR_DRIVINGDISTANCE "(" EdgesSQL "," RootVid "," distance ["," directed ]")".
```
```ebnf
pgr_drivingdistance = PGR_DRIVINGDISTANCE "(" EdgesSQL "," RootVid "," distance ["," directed ][","equicost]")".
```

PGR_DRIVINGDISTANCE函数基于Dijkstra算法计算从一个或多个起始点出发在给定距离限制内能够到达的所有终点或区域。


本函数作为表函数使用，返回包含以下列的表：

| 列名 | 数据类型 | 描述 |
| ---------- | ------ | ------ |
| SEQ      | INTEGER  | 路径的序号，是从1开始的顺序值   |
| FROM_V     | BIGINT | 起始点的标识ID，仅equicost为TRUE时存在该列  |
| NODE     | BIGINT | 从起始点出发的指定距离（distance参数）内的终点标识符 |
| EDGE     | BIGINT | 到达该终点（NODE列）所用的边的标识符<br/>值为0表示起始点=终点 |
| COST    | DOUBLE | 起始点-终点的边成本 |
| AGG_COST  | DOUBLE   | 起始点-终点的总开销 |




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





**RootVid**

起始点的标识符，其值须为BIGINT类型以及能够转换成BIGINT的类型。

当rootVid为NULL时，函数返回NULL。

**RootVids**

起始点的标识数组，其值须为由BIGINT类型构成的ARRAY类型。

*   如果数组中存在为NULL的元素，函数将报错。

*   当rootVids为NULL时，函数返回NULL。


**distance**

距离限制，其值为DOUBLE类型以及能够转换成DOUBLE的类型。

当distance为NULL时，函数返回NULL。



**directed**

指定EdgesSQL查询的图是否为有向图，其值为BOOLEAN类型以及能够转换成BOOLEAN的类型。

默认值为TRUE（有向图），若设置为FALSE则表示EdgesSQL查询的图为无向图。



**equicost**

指定是否仅保留距离最短的路径数据，其值须为BOOLEAN类型以及能够转换成BOOLEAN的类型。

默认值为 FALSE（不清理数据），若设置为TRUE要求输入边表必须包含多个起始顶点，且仅保留距离最短的路径相关数据。

示例（HEAP表）

```sql
SQL> SELECT * FROM pgr_drivingDistance('SELECT id, source, target, cost, reverse_cost FROM edges',1, 5.1);

                  SEQ                  NODE                  EDGE        COST    AGG_COST
--------------------- --------------------- --------------------- ----------- -----------
                    1                     1                    -1           0           0
                    2                     2                     1    1.0E+000    1.0E+000
                    3                     5                     4    1.2E+000    1.2E+000
                    4                     6                     5    8.0E-001    2.0E+000
                    5                     3                     2    1.5E+000    2.5E+000
                    6                     4                     6    1.7E+000    3.7E+000
                    
SQL> SELECT * FROM pgr_drivingDistance('SELECT id, source, target, cost, reverse_cost FROM edges',array(1, 2), 5.1, false, false);

                  SEQ                FROM_V                  NODE                  EDGE        COST    AGG_COST
--------------------- --------------------- --------------------- --------------------- ----------- -----------
                    1                     1                     1                    -1           0           0
                    2                     1                     2                     1    1.0E+000    1.0E+000
                    3                     1                     5                     4    1.2E+000    1.2E+000
                    4                     1                     6                     5    8.0E-001    2.0E+000
                    5                     1                     3                     2    1.5E+000    2.5E+000
                    6                     1                     4                     6    1.7E+000    3.7E+000
                    7                     2                     2                    -1           0           0
                    8                     2                     1                     1    1.0E+000    1.0E+000
                    9                     2                     3                     2    1.5E+000    1.5E+000
                   10                     2                     5                     4    1.2E+000    2.2E+000
                   11                     2                     6                     5    8.0E-001    3.0E+000
                   12                     2                     4                     3    2.0E+000    3.5E+000

SQL> SELECT * FROM pgr_drivingDistance('SELECT id, source, target, cost, reverse_cost FROM edges',array(1, 2), 5.1, false, true);

                  SEQ                FROM_V                  NODE                  EDGE        COST    AGG_COST
--------------------- --------------------- --------------------- --------------------- ----------- -----------
                    1                     1                     1                    -1           0           0
                    2                     1                     5                     4    1.2E+000    1.2E+000
                    3                     1                     6                     5    8.0E-001    2.0E+000
                    4                     2                     2                    -1           0           0
                    5                     2                     3                     2    1.5E+000    1.5E+000
                    6                     2                     4                     3    2.0E+000    3.5E+000



-- 本函数不支持绑定参数，报错YAS-07204 no function matches the given name and argument types
set serveroutput on;
DROP TABLE if exists   main_edges_ydbrd3621_84;
CREATE TABLE main_edges_ydbrd3621_84 (
    id int GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    source int NOT NULL,
    target int NOT NULL,
    cost int NOT NULL,
    reverse_cost int,
    category_id int,
    region_id int,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
-- 创建分类表
DROP TABLE if exists   categories_ydbrd3621_84;
CREATE TABLE categories_ydbrd3621_84 (
    id int GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    priority int DEFAULT 1,
    is_active int DEFAULT 1
);
-- 创建区域表
DROP TABLE if exists   regions_ydbrd3621_84;
CREATE TABLE regions_ydbrd3621_84 (
    id int GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    population int,
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
