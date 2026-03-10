```ebnf+diagram
st_boundary::= ST_BOUNDARY "(" geometry ")"
```

ST_BOUNDARY函数用于计算输入的geometry的组合边界，返回值为ST_GEOMETRY类型数据。

该函数根据输入geometry的不同数据类型，返回不同的边界类型：

- POINT和MULTIPOINT类型的边界为空，返回值为GEOMETRYCOLLECTION EMPTY。
- LINESTRING和MULTILINESTRING类型的边界为端点，返回值为MULTIPOINT类型，如输入闭环线则返回MULTIPOINT EMPTY。
- POLYGON和MULTIPOLYGON类型的边界为分割外部和内部的线性环，返回值为LINESTRING类型。

对于LINESTRING和MULTILINESTRING类型的输入，本函数计算时仅计算x、y坐标，z坐标不参与计算，即如输入值的端点x、y坐标相等时，则返回MULTIPOINT EMPTY；输出时间点按照x坐标从负到正的顺序，y坐标从负到正的顺序输出，输出值包含z坐标。

**geometry**

[通用表达式](../../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据，但不能为GEOMETRY COLLECTION类型。

当输入的参数存在NULL时，函数返回NULL。

示例（HEAP表）

```sql
--ST_BOUNDARY函数根据给定的GEOMETRY返回一个ST_GEOMETRY数据
--输入值为POINT类型
SELECT ST_ASTEXT(ST_BOUNDARY(ST_GeomFromText('POINT(2 6 9 5)'))) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
GEOMETRYCOLLECTION EMPTY  

--输入值为LINESTRING类型
SELECT ST_ASTEXT(ST_BOUNDARY(ST_GeomFromText('LINESTRING(1 1, 0 0, -1 1)')),0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
MULTIPOINT (1 1, -1 1) 

--输入值为POLYGON类型
SELECT ST_AsText(ST_Boundary(ST_GeomFromText('POLYGON((1 1, 0 0, -1 1, 1 1))')),0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
LINESTRING (1 1, 0 0, -1 1, 1 1)  

--参数包含NULL
SELECT ST_ASTEXT(ST_BOUNDARY(ST_GeomFromText(null, 4316))) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
              
```
