```ebnf+diagram
pgr_drivingdistance::= PGR_DRIVINGDISTANCE "(" EdgesSQL "," RootVid "," distance ["," directed ]")"
```
```ebnf+diagram
pgr_drivingdistance::= PGR_DRIVINGDISTANCE "(" EdgesSQL "," RootVid "," distance ["," directed ][","equicost]")"
```

The PGR_DRIVINGDISTANCE function uses Dijkstra's algorithm to calculate all reachable ending  or areas from one or more starting vertices within a specified distance limit.


This function is used as a table function, returning a table composed of columns:

| Column Name | Data Type |Description |
| ---------- | ------ | ------ |
| SEQ      | INTEGER  | Path sequence number, starting from 1.  |
| FROM_V     | BIGINT | Starting vertex identifier — exists only when equicost is TRUE. |
| NODE     | BIGINT | Identifier of the destination vertex reachable within the specified distance from the starting vertex. |
| EDGE     | BIGINT | Identifier of the edges used to reach the destination vertex (NODE column).<br/>A value of `0` indicates that the starting vertex equals the destination vertex. |
| COST    | DOUBLE | Cost of the edge from the starting vertex to the destination vertex. |
| AGG_COST  | DOUBLE   | Total cost from the starting vertex to the destination vertex.   |




**EdgesSQL**

The SQL statement for querying graph data. Its value must be of type VARCHAR or another type convertible to VARCHAR.

*   The total size of all projected columns after EdgeSQL execution shall not exceed 63K.

*   The `EdgesSQL` must retrieve the following information:

    | Column Name   | Supported Data Types | Description   |
    | ------------ | ----------------- | ------------------------- |
    | id           | TINYINT, SMALLINT, INT, or BIGINT | Edge identifier               |
    | source       | TINYINT, SMALLINT, INT, or BIGINT | Identifier of the first endpoint vertex of the edge         |
    | target       | TINYINT, SMALLINT, INT, or BIGINT | Identifier of the second endpoint vertex of the edge         |
    | cost         | TINYINT, SMALLINT, INT, BIGINT, FLOAT, DOUBLE, or NUMBER | Weight from `source` to `target`      |
    | reverse_cost | TINYINT, SMALLINT, INT, BIGINT, FLOAT, DOUBLE, or NUMBER | Weight from `target` to `source`; default value is -1 <br/> If `reverse_cost` is negative, it indicates that the path from `target` to `source` is not reachable. |

*   When `EdgesSQL` is NULL, the function returns NULL.





**RootVid**

The identifier of the starting vertex. Its value must be of type `BIGINT` or another type convertible to `BIGINT`.

When `rootVid` is NULL, the function returns NULL.

**RootVids**

The array of starting vertex identifiers. Its value must be of type `ARRAY(BIGINT)`.

*   If the array contains any `NULL` elements, the function will return an error.

*   When `rootVids` is NULL, the function returns NULL.


**distance**

The distance limit, whose value must be of type DOUBLE or another type convertible to DOUBLE.

When `distance` is NULL, the function returns NULL.



**directed**

Specifies whether the graph queried by `EdgesSQL` is directed. Its value must be of type `BOOLEAN` or another type convertible to `BOOLEAN`.

Default value is `TRUE` (directed graph); if set to `FALSE`, it indicates that the graph queried by `EdgesSQL` is undirected.



**equicost**

Specifies whether only the data with the shortest distance to the same destination should be retained. Its value must be of type BOOLEAN or another type convertible to BOOLEAN.

Default value is `FALSE` (no data cleanup). If set to `TRUE`, the input edge table must contain multiple starting vertices, and only the data corresponding to the shortest distance will be retained.

***Example*** for Heap tables

```sql
SELECT * FROM pgr_drivingDistance('SELECT id, source, target, cost, reverse_cost FROM edges',1, 5.1);

                  SEQ                  NODE                  EDGE        COST    AGG_COST
--------------------- --------------------- --------------------- ----------- -----------
                    1                     1                    -1           0           0
                    2                     2                     1    1.0E+000    1.0E+000
                    3                     5                     4    1.2E+000    1.2E+000
                    4                     6                     5    8.0E-001    2.0E+000
                    5                     3                     2    1.5E+000    2.5E+000
                    6                     4                     6    1.7E+000    3.7E+000
                    
SELECT * FROM pgr_drivingDistance('SELECT id, source, target, cost, reverse_cost FROM edges',ARRAY(1, 2), 5.1, false, false);

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

SELECT * FROM pgr_drivingDistance('SELECT id, source, target, cost, reverse_cost FROM edges',ARRAY(1, 2), 5.1, false, true);

                  SEQ                FROM_V                  NODE                  EDGE        COST    AGG_COST
--------------------- --------------------- --------------------- --------------------- ----------- -----------
                    1                     1                     1                    -1           0           0
                    2                     1                     5                     4    1.2E+000    1.2E+000
                    3                     1                     6                     5    8.0E-001    2.0E+000
                    4                     2                     2                    -1           0           0
                    5                     2                     3                     2    1.5E+000    1.5E+000
                    6                     2                     4                     3    2.0E+000    3.5E+000



-- This function does not support bound parameters, report errror YAS-07204 no function matches the given name and argument types
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
-- Create classification table
DROP TABLE IF EXISTS   categories_ydbrd3621_84;
CREATE TABLE categories_ydbrd3621_84 (
    id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    priority INT DEFAULT 1,
    is_active INT DEFAULT 1
);
-- Create region table
DROP TABLE IF EXISTS   regions_ydbrd3621_84;
CREATE TABLE regions_ydbrd3621_84 (
    id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    population INT,
    area_code VARCHAR(10)
);
-- Insert basic data
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
    --  BUILD dynamic SQL string (avoid bind variable issues IN SQL string)
    DECLARE
        v_dynamic_edges_sql VARCHAR2(1000);
    BEGIN
        -- BUILD SQL string WITH specific VALUES
        v_dynamic_edges_sql := 'SELECT id, source, target, cost FROM main_edges_ydbrd3621_84 WHERE cost <= ' || v_max_cost;
        
        DBMS_OUTPUT.PUT_LINE('Used edge query SQL: ' || v_dynamic_edges_sql);
        DBMS_OUTPUT.PUT_LINE('Starting point: ' || v_start_node || ', Distance Limit: ' || v_distance ||
                           ', Directed: ' || CASE WHEN v_directed THEN 'Yes' ELSE 'No' END);
        
        -- Calculate reachable node count
        EXECUTE IMMEDIATE '
            SELECT COUNT(*) FROM pgr_drivingDistance(:edges_sql, :start_vid, :distance, :directed_flag)'
        INTO v_result_count
        USING v_dynamic_edges_sql, v_start_node, v_distance, v_directed;
        
        DBMS_OUTPUT.PUT_LINE('Comprehensive test for reachable node count:' || v_result_count);
        -- IF there are reachable nodes, display detailed results
        IF v_result_count > 0 THEN
            DBMS_OUTPUT.PUT_LINE('Details of reachable nodes:');
            
            DECLARE
                v_sql VARCHAR2(2000);
                v_directed_str VARCHAR2(10);
            BEGIN
                -- BUILD executable SQL
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
