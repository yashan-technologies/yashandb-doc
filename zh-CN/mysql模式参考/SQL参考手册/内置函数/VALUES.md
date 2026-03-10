```ebnf+diagram
values::= VALUES "(" col_name ")"
```

如需对存在unique约束的列执行ON DUPLICATE KEY UPDATE的INSERT语句，可使用VALUES函数将指定列的INSERT语句插入值引用为UPDATE语句更新值，避免因unique约束冲突而报错。

示例（HEAP表）

```sql
-- 创建表area0，字段area_no存在唯一性约束
CREATE TABLE area0(area_no INT UNIQUE, area_name VARCHAR(100));

INSERT INTO area0 VALUES(0, 'Shenzhen'), (1, 'Shanghai');

SELECT area_no,area_name FROM area0;
     area_no area_name
------------ -------------------
           0 Shenzhen
           1 Shanghai


-- 与unique约束冲突报错
INSERT INTO area0 VALUES(0, 'Chengdu');

YAS-02030 unique constraint(SALES.SYS_C_244) violated

-- 对表中存在冲突的行执行update
INSERT INTO area0 VALUES(0, 'Chengdu') ON DUPLICATE KEY UPDATE area_no = VALUES(area_no), area_name = VALUES(area_name);

-- 更新成功
SELECT area_no,area_name FROM area0;
     area_no area_name
------------ --------------------
           0 Chengdu
           1 Shanghai

```
