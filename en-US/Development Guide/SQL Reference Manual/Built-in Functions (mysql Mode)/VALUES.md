```ebnf+diagram
values::= VALUES "(" col_name ")"
```

To execute an INSERT statement with ON DUPLICATE KEY UPDATE for columns that have a unique constraint, you can use the VALUES function to reference the values being inserted in the UPDATE clause. This avoids errors caused by conflicts with the unique constraint.

***Example*** for Standalone Deployment Heap tables

```sql
-- Create table area0, with a unique constraint on the area_no field
CREATE TABLE area0(area_no INT UNIQUE, area_name VARCHAR(100));

INSERT INTO area0 VALUES(0, 'Shenzhen'), (1, 'Shanghai');

SELECT area_no,area_name FROM area0;
     AREA_NO AREA_NAME
------------ -------------------
           0 Shenzhen
           1 Shanghai


-- Error due to unique constraint conflict
INSERT INTO area0 VALUES(0, 'Chengdu');

YAS-02030 unique constraint(SALES.SYS_C_244) violated

-- Perform update on the conflicting row
INSERT INTO area0 VALUES(0, 'Chengdu') ON DUPLICATE KEY UPDATE area_no = VALUES(area_no), area_name = VALUES(area_name);

-- Update successful
SELECT area_no,area_name FROM area0;
     AREA_NO AREA_NAME
------------ --------------------
           0 Chengdu
           1 Shanghai

```
