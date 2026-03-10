```ebnf+diagram
st_makepoint::= ST_MAKEPOINT "(" x "," y [ "," z [ "," m ] ] ")"
```

ST_MAKEPOINT函数根据输入的x、y和可选的z和m，返回对应坐标的POINT数据。

**x、y、z、m**

表示坐标，参数类型为数值型，支持可以转换为DOUBLE类型的CHAR、VARCHAR类型（转换失败返回Invalid Number错误），使用其他数据类型则返回错误。

当输入的参数存在NULL时，函数返回NULL。

示例（HEAP表）

```sql
SELECT ST_AsText(ST_MakePoint(1, 2), 0) res FROM DUAL;

RES                                            
---------------------------------------------------------------- 
POINT (1 2)                                                     

SELECT ST_AsText(ST_MakePoint(1, 2, 3), 0) res FROM DUAL;

RES                                        
---------------------------------------------------------------- 
POINT Z (1 2 3)                                                 

SELECT ST_AsText(ST_MakePoint(1, 2, 3, 4), 0) res FROM DUAL;

RES                                        
---------------------------------------------------------------- 
POINT Z (1 2 3)
```