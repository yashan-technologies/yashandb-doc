```ebnf
empty_clob = EMPTY_CLOB "("")".
```

The EMPTY_CLOB function is commonly used to initialize a CLOB variable, often in INSERT and UPDATE statements, and it returns an empty CLOB.

This function follows these rules:

- The function parameter is empty.
- When using the LENGTH() function to query the output value of this function, the return value is 0.
- YashanDB supports directly querying the EMPTY_CLOB() function, returning an empty CLOB.
- This function does not support vectorization calculation.

***Example*** for Heap tables

```sql
CREATE TABLE LOB_EMPTY_CLOB (clob1 CLOB,blob2 BLOB);
INSERT INTO LOB_EMPTY_CLOB VALUES('1234','234');
SELECT clob1,blob2 FROM LOB_EMPTY_CLOB;

CLOB1                                                            BLOB2                                                            
---------------------------------------------------------------- ---------------------------------------------------------------- 
1234                                                             0234                                 
      
UPDATE LOB_EMPTY_CLOB SET clob1 = empty_clob(), blob2 = empty_blob();
SELECT clob1,blob2 FROM LOB_EMPTY_CLOB;

CLOB1                                                            BLOB2                                                            
---------------------------------------------------------------- ---------------------------------------------------------------- 
```

***Example*** for Heap tables

```sql
-- Query LENGTH(EMPTY_CLOB ())
SELECT LENGTH(EMPTY_CLOB()) res FROM DUAL;

                 RES
--------------------
                   0
```
