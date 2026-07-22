```ebnf
st_geometricmedian = ST_GEOMETRICMEDIAN "(" geometry [ "," tolerance ] [ "," maxIter ] [ "," failIfNotConverged ] ")".
```

The functionality of the ST_GEOMETRICMEDIAN function is to compute the geometric median of MULTIPOINT data using the Weiszfeld algorithm. It will ultimately return a POINT data, which has the minimum total distance to all POINTS in the MULTIPOINT.

**geometry**

[General expression](../../General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data, following these rules:

* When this parameter is NULL, the function returns NULL.
* The dimensionality of the output POINT data is determined by the maximum dimension of the input MULTIPOINT data and will not exceed three dimensions.
* If the input geometry is of POINT type, it will return that POINT; if the input is MULTIPOINT EMPTY, it will return POINT EMPTY; other types will result in an error.

**tolerance**

Tolerance represents the tolerance, its value is DOUBLE type. The Weiszfeld algorithm will continuously iterate until the distance variation between consecutive iterations is less than the given tolerance. This parameter follows these rules:

* This parameter can be omitted; if omitted or input as NULL, a tolerance will be calculated based on the bounding box of the input geometry.
* Supports data types that can be implicitly converted to DOUBLE.
* Tolerance only supports non-negative numbers; otherwise, an error will occur.

**maxIter**

MaxIter represents the maximum number of iterations, its value is INT type, following these rules:

* This parameter can be omitted, with a default value of 10000.
* Supports data types that can be implicitly converted to INT.
* MaxIter can only be a positive integer; if a negative number or NULL is input, an error will occur.
* When the failIfNotConverged parameter is TRUE, if maxIter iterations are performed and the second condition is still not met, an error will occur.
* When the failIfNotConverged parameter is FALSE, even if maxIter iterations are performed and the second condition is still not met, no error will occur.

**failIfNotConverged**

FailIfNotConverged indicates whether to report an error if the iteration count exceeds maxIter; its value is BOOLEAN type, following these rules:

* This parameter can be omitted, with a default value of FALSE; inputting NULL is treated as FALSE.
* Supports data types that can be implicitly converted to BOOLEAN.

***Example*** for Heap tables

```sql
--ST_GEOMFROMTEXT function returns ST_GEOMETRY data based on the given WKT and SRID
SELECT ST_AsText(ST_GeometricMedian(ST_GeomFromText('MULTIPOINT(1 1, 2 2, 3 3)')), 0) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
POINT (2 2)                                                     

SELECT ST_AsText(ST_GeometricMedian(ST_GeomFromText('MULTIPOINT(1 1, 2 2, 3 3 3)')), 0) res FROM DUAL;

RES                                            
---------------------------------------------------------------- 
POINT Z (2 2 0)                                                 

SELECT ST_AsText(ST_GeometricMedian(ST_GeomFromText('MULTIPOINT(1 1, 2 2, 3 3 3)'), NULL, 0, TRUE), 0) res FROM DUAL;

YAS-07202 plugin execution error, Median failed to converge within 1e-08 after 0 iterations.
```
