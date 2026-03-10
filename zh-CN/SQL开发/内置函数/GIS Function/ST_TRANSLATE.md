```ebnf+diagram
st_translate::= ST_TRANSLATE "(" geometry "," delta_x"," delta_y ")"
```

```ebnf+diagram
st_translate::= ST_TRANSLATE "(" geometry "," delta_x "," delta_y "," delta_z ")"
```

ST_TRANSLATE函数用于平移二维或三维空间对象，将指定的空间对象（如点、线、面、多边形等）沿 X、Y、Z轴方向按指定距离移动，保持对象的形状、大小和方向不变，仅改变其在空间中的位置。

**geometry**

[通用表达式](../../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

当输入的参数存在NULL时，函数返回NULL。

**delta_x/delta_y/delta_z**

设置沿每个坐标轴平移的距离，要求输入数据类型为DOUBLE类型，或者可以隐式转换为DOUBLE的其他数据类型。

- 当输入的参数存在NULL时，函数返回NULL。
- 当输入的参数超出DOUBLE数据类型精度范围（如nan、inf和-inf）时，对应坐标轴的函数计算结果不影响其他坐标轴计算结果。

示例（HEAP表）

```sql
SELECT ST_AsText(ST_TRANSLATE(ST_GeomFromText('POINT(1 1)'), 5, 3)) res FROM dual;

RES
----------------------------------------------------------------
POINT (6.000000000000000 4.000000000000000)

SELECT ST_AsText(ST_TRANSLATE(ST_GeomFromText('POINT(1 1 1)'), 5, 3,1)) res FROM dual;

RES
----------------------------------------------------------------
POINT Z (6.000000000000000 4.000000000000000 2.000000000000000)

SELECT st_astext(st_translate(NULL, 1, 2)) FROM dual;

ST_ASTEXT(ST_TRANSLATE(NULL,1,2))                                
---------------------------------------------------------------- 
                                                                
SELECT st_astext(st_translate(st_geomfromtext('point(1 2 3)', 4326), 2, 2, CAST('inf' AS DOUBLE))) res FROM dual;

RES
---------------------------------------------------------------- 
POINT Z (3.000000000000000 4.000000000000000 inf)     
```
