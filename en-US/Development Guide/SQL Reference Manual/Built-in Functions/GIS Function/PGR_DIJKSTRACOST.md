```ebnf
pgr_dijkstraCost = PGR_DIJKSTRACOST "(" EdgesSQL ","(startVid|startVids) "," (endVid|endVids) ["," directed ]")".
```
```ebnf
pgr_dijkstraCost = PGR_DIJKSTRACOST "(" EdgesSQL "," CombinationsSQL ["," directed ]")".
```

The PGR_DIJKSTRA_COST function uses Dijkstra's algorithm to the total cost of the shortest path from a starting vertex to an ending vertex in the graph, and returns a table containing the following columns: `START_VID` (starting vertex ID), `END_VID` (ending vertex ID), and `AGG_COST` (total cost).



*   This function supports the following computation scenarios:

    *   Single TO Single：Calculate the shortest path from the single starting vertex `startVid` to the single ending vertex `endVid`.

    *   Single TO Multiple: Calculate the shortest path from the single starting vertex `startVid` to multiple ending vertices `endVids`.

    *   MULTIPLE TO SINGLE: Calculate the shortest paths from multiple starting vertices `startVids` to a single ending vertex `endVid`.

    *   MULTIPLE TO MULTIPLE: Calculate the shortest paths from multiple starting vertices `startVids` to multiple ending vertices `endVids`.

*   This function supports both undirected and directed graphs, and allows users to specify edge weights.



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





**CombinationsSQL**

The SQL statement for querying the start and end vertices. Its value must be of type VARCHAR or another type convertible to VARCHAR.

*   The `CombinationsSQL` must retrieve the following information:

    | Column Name   | Supported Data Types | Description   |
    | ------------ | ----------------- | ------------------------- |
    | source | TINYINT, SMALLINT, INT, or BIGINT | Identifier of the starting vertex |
    | target | TINYINT, SMALLINT, INT, or BIGINT | Identifier of the ending vertex |

*   When `CombinationsSQL` is NULL, the function returns NULL.

**startVid**

The identifier of the starting vertex. Its value must be of type `BIGINT` or another type convertible to `BIGINT`.

When startVid is NULL, the function returns NULL.

**startVids**

The array of starting vertex identifiers. Its value must be of type `ARRAY(BIGINT)`.

*   If the array contains any `NULL` elements, the function will return an error.

*   When startVids is NULL, the function returns NULL.

**endVid**

The identifier of the ending vertex. Its value must be of type `BIGINT` or another type convertible to `BIGINT`.

When endVid is NULL, the function returns NULL.

**endVids**

The array of ending vertex identifiers. Its value must be of type `ARRAY(BIGINT)`.

*   If the array contains any `NULL` elements, the function will return an error.

*   When endVids is NULL, the function returns NULL.



**directed**

Specifies whether the graph queried by `EdgesSQL` is directed. Its value must be of type `BOOLEAN` or another type convertible to `BOOLEAN`.

Default value is `TRUE` (directed graph); if set to `FALSE`, it indicates that the graph queried by `EdgesSQL` is undirected.




***Example*** for Heap tables

```sql
-- Query pgr_dijkstraCost table
SQL> SELECT * FROM pgr_dijkstraCost('SELECT id, source, target, cost, reverse_cost FROM edges',1, 2);

            START_VID               END_VID    AGG_COST
--------------------- --------------------- -----------
                    1                     2    1.0E+000
              
SQL> SELECT * FROM pgr_dijkstraCost('SELECT id, source, target, cost, reverse_cost FROM edges',1, 3);

            START_VID               END_VID    AGG_COST
--------------------- --------------------- -----------
                    1                     3    2.5E+000
              
SQL> SELECT * FROM pgr_dijkstraCost('SELECT id, source, target, cost, reverse_cost FROM edges',1, array(2, 3));

            START_VID               END_VID    AGG_COST
--------------------- --------------------- -----------
                    1                     2    1.0E+000
                    1                     3    2.5E+000

SQL> SELECT * FROM pgr_dijkstraCost('SELECT id, source, target, cost, reverse_cost FROM edges',array(2, 3), 1);

            START_VID               END_VID    AGG_COST
--------------------- --------------------- -----------
                    2                     1    1.0E+000
                    3                     1    2.5E+000
SQL> SELECT * FROM pgr_dijkstraCost('SELECT id, source, target, cost, reverse_cost FROM edges',array(2, 3), array(4, 5));

            START_VID               END_VID    AGG_COST
--------------------- --------------------- -----------
                    2                     4    3.5E+000
                    2                     5    2.2E+000
                    3                     4    2.0E+000
                    3                     5    3.7E+000



-- This function does not support bound parameters, report errror YAS-07204 no function matches the given name and argument types
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
-- Create classification table
DROP TABLE if exists   categories_ydbrd3621_84;
CREATE TABLE categories_ydbrd3621_84 (
    id int GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    priority int DEFAULT 1,
    is_active int DEFAULT 1
);
-- Create region table
DROP TABLE if exists   regions_ydbrd3621_84;
CREATE TABLE regions_ydbrd3621_84 (
    id int GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    population int,
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
    --  Build dynamic SQL string (avoid bind variable issues in SQL string)
    DECLARE
        v_dynamic_edges_sql VARCHAR2(1000);
    BEGIN
        -- Build SQL string with specific values
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
        -- If there are reachable nodes, display detailed results
        IF v_result_count > 0 THEN
            DBMS_OUTPUT.PUT_LINE('Details of reachable nodes:');
            
            DECLARE
                v_sql VARCHAR2(2000);
                v_directed_str VARCHAR2(10);
            BEGIN
                -- Build executable SQL
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
