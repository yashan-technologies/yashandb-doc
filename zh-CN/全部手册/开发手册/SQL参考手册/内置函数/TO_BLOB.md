```ebnf+diagram
to_blob::= TO_BLOB "(" expr ["," mime_type] ")"
```

TO_BLOB函数将[expr](../通用SQL语法/expr)转换为BLOB类型的数据。

本函数仅支持基于HEAP表使用。

**expr**

通用表达式，需要被转换的数据，只能为BFILE、BLOB、RAW、UROWID或STRING类型。

当expr的值为NULL时，函数返回NULL。

**mime_type**

该参数仅用于语法兼容，无实际意义。

仅当expr为BLOB/BFILE类型时，可以指定mime_type。

示例（HEAP表）

```sql
-- RAW
DROP TABLE IF EXISTS to_blob_t;
CREATE TABLE to_blob_t (id INT, raw_col RAW(100));
INSERT INTO to_blob_t VALUES(1,null);
INSERT INTO to_blob_t VALUES(2,'123');

SELECT TO_BLOB(raw_col) FROM to_blob_t WHERE id = 1;

TO_BLOB(RAW_COL)
----------------------------------------------------------------

SELECT TO_BLOB(raw_col) FROM to_blob_t WHERE id = 2;

TO_BLOB(RAW_COL)
----------------------------------------------------------------
0123

-- BFILE
CREATE OR REPLACE DIRECTORY MY_DIR AS '/data/seyashan';
!echo "this is test bfile file here" >> /data/seyashan/to_blob.txt  

ALTER TABLE to_blob_t ADD(filename VARCHAR(100), bf_col BFILE);
INSERT INTO to_blob_t VALUES(3,'456','to_blob.txt',BFILENAME('MY_DIR','to_blob.txt'));

SELECT TO_BLOB(bf_col) FROM to_blob_t WHERE id = 3;

TO_BLOB(BF_COL)
----------------------------------------------------------------
746869732069732074657374206266696C652066696C6520686572650A746869732069732074657374206266696C652066696C6520686572650A746869732069732074657374206266696C652066696C6520686572650A
```