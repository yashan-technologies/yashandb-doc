```ebnf+diagram
empty_blob::= EMPTY_BLOB "("")"
```

EMPTY_BLOB函数常用来初始化一个BLOB变量，常用在INSERT和UPDATE语句中，返回值为一个空的BLOB。

本函数遵循如下规则：

- 函数参数为空。
- 使用LENGTH()函数查询本函数输出值长度时，返回值为0。
- YashanDB支持直接查询EMPTY_BLOB()函数，返回值为一个空的BLOB。
- 本函数不支持向量化计算。

示例（HEAP表）

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

示例（HEAP表）

```sql
-- 查询LENGTH(EMPTY_BLOB())
SELECT LENGTH(EMPTY_BLOB()) res FROM DUAL;

                 RES
--------------------
                   0
```
