**storage_clause::=**

```ebnf+diagram
syntax::= STORAGE "(" 
    (
        ( 
            INITIAL size_clause | NEXT size_clause | MINEXTENTS integer | (MAXEXTENTS (integer | UNLIMITED)) | maxsize_clause | PCTINCREASE integer | FREELISTS integer | 
            FREELIST GROUPS integer | BUFFER_POOL (KEEP | RECYCLE | DEFAULT) | FLASH_CACHE (KEEP|NONE|DEFAULT) | CELL_FLASH_CACHE (KEEP | NONE| DEFAULT)
        ) 
        {" " 
            ( 
                INITIAL size_clause | NEXT size_clause | MINEXTENTS integer | (MAXEXTENTS (integer | UNLIMITED)) | maxsize_clause | PCTINCREASE integer | FREELISTS integer | 
                FREELIST GROUPS integer | BUFFER_POOL (KEEP | RECYCLE | DEFAULT) | FLASH_CACHE (KEEP|NONE|DEFAULT) | CELL_FLASH_CACHE (KEEP | NONE| DEFAULT)
            )}
    ) 
")" 
```

[**size_clause/maxsize_clause**](size_clause)
[**size_clause/maxsize_clause**](size_clause)

storage_clause用于对segment的属性进行描述，在创建表或索引等对象时可使用此语句，但在YashanDB中本语句无实际含义，只用作语法兼容。

示例（HEAP表，单机TAC表）

```sql
--创建分区表，为表和分区都指定storage属性
CREATE TABLE part_storage(a INT, b VARCHAR(4000)) 
PARTITION BY RANGE(a, b)
(
	PARTITION p1 VALUES LESS THAN(1, 'a') STORAGE(INITIAL 0 MAXSIZE 1M NEXT 0),
	PARTITION p2 VALUES LESS THAN(10, 'c') STORAGE(MINEXTENTS 1 MAXEXTENTS 10 PCTINCREASE 0),
	PARTITION p3 VALUES LESS THAN(MAXVALUE, MAXVALUE) STORAGE(INITIAL 0 MAXSIZE 1M NEXT 0 MINEXTENTS 1 MAXEXTENTS 10 PCTINCREASE 0 FREELIST GROUPS 20 BUFFER_POOL RECYCLE FLASH_CACHE KEEP CELL_FLASH_CACHE DEFAULT)
) 
STORAGE(INITIAL 63K MAXSIZE 10M NEXT 12k MINEXTENTS 1 MAXEXTENTS 10 PCTINCREASE 0 FREELISTS 10);

--创建索引，为索引指定storage属性
CREATE INDEX idx_part_storage_2 
ON part_storage(b) 
STORAGE(MINEXTENTS 1 MAXEXTENTS 10 PCTINCREASE 0);
```
