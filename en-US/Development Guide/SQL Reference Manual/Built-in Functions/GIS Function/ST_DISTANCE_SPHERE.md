```ebnf
st_distance_sphere = ST_DISTANCE_SPHERE "(" x_lng "," x_lat "," y_lng "," y_lat ")".
```

The ST_DISTANCE_SPHERE function returns the spherical distance between two points on the Earth based on the longitude and latitude of input points X and Y, with the result measured in meters.

**lng**

[General Expression](../../General SQL Syntax/expr), which must be a valid DOUBLE type value, represents longitude, with a valid range of [-180,180]; otherwise, it returns NULL.

**lat**

[General Expression](../../General SQL Syntax/expr), which must be a valid DOUBLE type value, represents latitude, with a valid range of [-90,90]; otherwise, it returns NULL.

When any input parameter is NULL, the function returns NULL.

***Example***

```sql
select ST_Distance_Sphere(120, 0, 120, 0) res from dual;

        RES 
----------- 
          0

select ST_Distance_Sphere(150, 0, 12, 5) res from dual;

        RES 
----------- 
1.532E+007

select ST_Distance_Sphere(null, 0, 0, 0) res from dual;

        RES 
----------- 
```
