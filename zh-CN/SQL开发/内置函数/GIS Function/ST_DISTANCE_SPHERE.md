```ebnf+diagram
st_distance_sphere::= ST_DISTANCE_SPHERE "(" x_lng "," x_lat "," y_lng "," y_lat ")"
```

ST_DISTANCE_SPHERE函数根据输入的X点和Y点的经度纬度，返回地球上两点之间的球面距离，单位为米。

**lng**

[通用表达式](../../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，其值必须为有效的DOUBLE类型的数据，含义是经度，取值范围是[-180,180]，否则返回NULL。

**lat**

[通用表达式](../../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)，其值必须为有效的DOUBLE类型的数据，含义是纬度，取值范围是[-90,90]，否则返回NULL。

当输入的参数存在NULL时，函数返回NULL。

示例

```sql
SELECT ST_Distance_Sphere(120, 0, 120, 0) res FROM dual;

        RES 
----------- 
          0

SELECT ST_Distance_Sphere(150, 0, 12, 5) res FROM dual;

        RES 
----------- 
1.532E+007

SELECT ST_Distance_Sphere(null, 0, 0, 0) res FROM dual;

        RES 
----------- 
```
