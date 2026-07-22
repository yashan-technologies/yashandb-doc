```ebnf
to_blob = TO_BLOB "(" expr ["," mime_type] ")".
```

The TO_BLOB function converts [expr](../General SQL Syntax/expr) to data of BLOB type.

This function only supports usage on HEAP tables.

**expr**

Common expression, data that needs to be converted, can only be of BFILE, BLOB, RAW, UROWID, or STRING type.

When the value of expr is NULL, the function returns NULL.

**mime_type**

This parameter is only for syntax compatibility and has no practical significance.

`mime_type` can only be specified when expr is of BLOB/BFILE type.

***Example*** for Heap tables

```sql
-- RAW
DROP TABLE IF EXISTS to_blob_t;
CREATE TABLE to_blob_t (id int, raw_col raw(100));
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

ALTER TABLE to_blob_t ADD(filename varchar(100), bf_col BFILE);
INSERT INTO to_blob_t VALUES(3,'456','to_blob.txt',BFILENAME('MY_DIR','to_blob.txt'));

SELECT TO_BLOB(bf_col) FROM to_blob_t WHERE id = 3;

TO_BLOB(BF_COL)
----------------------------------------------------------------
746869732069732074657374206266696C652066696C6520686572650A746869732069732074657374206266696C652066696C6520686572650A746869732069732074657374206266696C652066696C6520686572650A
```