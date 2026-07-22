```ebnf
st_envelope = ST_ENVELOPE "(" geometry ")".
```

ST_ENVELOPE函数用于计算输入的geometry的最小外包矩形，返回值为ST_GEOMETRY类型数据。

该函数根据输入geometry的不同，返回值的数据类型不同：

- 输入值为POINT类型时，返回值为POINT类型。
- 输入值为其他类型时，返回值为POLYGON类型。输入值为垂直线、水平线时，返回值为线形状的POLYGON类型数据。
- 输入值为任意类型EMPTY时，返回值为POINT EMPTY。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据，但不能为三维的ST_GEOMETRY类型数据。

当输入的参数存在NULL时，函数返回NULL。

示例（HEAP表）

```sql
--ST_ENVELOPE函数根据给定的GEOMETRY返回一个ST_GEOMETRY数据
--输入值为POINT类型
SELECT ST_ASTEXT(ST_ENVELOPE(ST_GEOMFROMTEXT('POINT(4 9)')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POINT (4 9)  

--输入值为MULTIPOINT类型
SELECT ST_ASTEXT(ST_ENVELOPE(ST_GEOMFROMTEXT('MULTIPOINT(4 9,3 12)')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POLYGON ((3 9, 4 9, 4 12, 3 12, 3 9)) 

--输入值为垂直线
SELECT ST_ASTEXT(ST_ENVELOPE(ST_GEOMFROMTEXT('LINESTRING(1 2,1 3,1 6)')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POLYGON ((1 2, 1 2, 1 6, 1 6, 1 2))    

--输入值为LINESTRING EMPTY
SELECT ST_ASTEXT(ST_ENVELOPE(ST_GEOMFROMTEXT('LINESTRING EMPTY')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POINT EMPTY 

--输入值为GEOMTERY COLLECTION类型
SELECT ST_ASTEXT(ST_ENVELOPE(ST_GeomFromText('GEOMETRYCOLLECTION (LINESTRING(55 75,125 150), POINT(20 80))')), 0) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
POLYGON ((20 75, 125 75, 125 150, 20 150, 20 75))                                         

--参数包含NULL
SELECT ST_ASTEXT(ST_ENVELOPE(ST_GeomFromText(null, 4316))) res from DUAL;

RES                                                              
---------------------------------------------------------------- 
             
```
