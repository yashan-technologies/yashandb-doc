```ebnf+diagram
st_scale::= ST_SCALE "(" geometry "," factor_x"," factor_y ")"
```

```ebnf+diagram
st_scale::= ST_SCALE "(" geometry "," factor_x "," factor_y "," factor_z ")"
```

ST_SCALE函数用于缩小或放大二维或三维空间对象，将指定的空间对象（如点、线、面、多边形等）沿X、Y、Z轴方向按指定比例进行放大或缩小，保持对象起始位置不变。

**geometry**

[通用表达式](../../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

当输入的参数存在NULL时，函数返回NULL。

**factor_x/factor_y/factor_z**

设置沿每个坐标轴缩小或放大的比例，要求输入数据类型为DOUBLE类型，或者可以隐式转换为DOUBLE的其他数据类型。

- 当输入的参数存在NULL时，函数返回NULL。
- 当输入的参数超出DOUBLE数据类型精度范围（如nan、inf和-inf）时，对应坐标轴的函数计算结果不影响其他坐标轴计算结果。


示例（HEAP表）

```sql
SELECT ST_AsText(ST_SCALE(ST_GeomFromText('POINT(2 2 1)'), 2, 3,1)) scale FROM dual;

SCALE
----------------------------------------------------------------
POINT Z (4.000000000000000 6.000000000000000 1.000000000000000)

SELECT ST_AsText(ST_SCALE(ST_GeomFromText('POINT(2 2)'), 2, 3)) scale FROM dual;

SCALE
----------------------------------------------------------------
POINT (4.000000000000000 6.000000000000000)

SELECT st_astext(st_scale(st_geomfromtext('point(1 2)', 4326), CAST('-inf' AS FLOAT), 2)) scale FROM dual;

SCALE
---------------------------------------------------------------- 
POINT (-inf 4.000000000000000)                                  

SELECT st_astext(st_scale(NULL, 1, 2)) FROM dual;

ST_ASTEXT(ST_SCALE(NULL,1,2))                                
---------------------------------------------------------------- 
                                                                
```
