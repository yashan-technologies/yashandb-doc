```ebnf+diagram
empty_blob::= EMPTY_BLOB "("")"
```

The EMPTY_BLOB function is commonly used to initialize a BLOB variable, often in INSERT and UPDATE statements, returning an empty BLOB.

This function follows these rules:

- The function parameter is empty.
- When using the LENGTH() function to query the output length of this function, the return value is 0.
- YashanDB supports direct querying of the EMPTY_BLOB() function, returning an empty BLOB.
- This function does not support vectorization calculation.

***Example*** for Heap tables

```sql
CREATE TABLE LOB_EMPTY_BLOB (clob1 CLOB,blob2 BLOB);
INSERT INTO LOB_EMPTY_BLOB VALUES('1234','234');
SELECT clob1,blob2 FROM LOB_EMPTY_BLOB;

CLOB1                                                            BLOB2                                                            
---------------------------------------------------------------- ---------------------------------------------------------------- 
1234                                                             0234                  
                                      
UPDATE LOB_EMPTY_BLOB SET clob1 = EMPTY_CLOB(), blob2 = EMPTY_BLOB();
SELECT clob1,blob2 FROM LOB_EMPTY_BLOB;

CLOB1                                                            BLOB2                                                            
---------------------------------------------------------------- ---------------------------------------------------------------- 

```

***Example*** for Heap tables

```sql
-- Query LENGTH(EMPTY_BLOB())
SELECT LENGTH(EMPTY_BLOB()) res FROM DUAL;

                 RES
--------------------
                   0
```
