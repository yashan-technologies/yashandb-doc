```ebnf+diagram
geometrytype::= GEOMETRYTYPE "(" geometry ")"
```

GEOMETRYTYPE函数用于打印输入的geometry的类型。

该函数会根据输入的geometry返回该geometry的数据类型，返回值为VARCHAR类型，返回值全为大写。

与[ST_GEOMETRYTYPE](ST_GEOMETRYTYPE)函数相比，GEOMETRYTYPE函数的返回值无ST_前缀且为全大写。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值须为有效的ST_GEOMETRY类型的数据。

当输入的参数存在NULL时，函数返回NULL。

示例（HEAP表）

```sql
--ST_GEOMFROMTEXT函数根据给定的WKT和SRID返回一个ST_GEOMETRY数据
SELECT GEOMETRYTYPE(ST_GeomFromText('POINT(-58.2687 29.149)',1356)) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POINT   
        
SELECT GEOMETRYTYPE(ST_GeomFromText('LINESTRING(1 6,3 2,9 7)',1356)) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
LINESTRING  

--参数包含NULL
SELECT GEOMETRYTYPE(ST_GeomFromText(null,1356)) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
                                                                      
```
