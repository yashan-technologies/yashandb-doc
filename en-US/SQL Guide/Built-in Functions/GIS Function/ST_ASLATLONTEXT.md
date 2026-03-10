```ebnf+diagram
st_aslatlontext::= ST_ASLATLONTEXT "(" geometry "," format ")"
```

The ST_ASLATLONTEXT function returns the degree, minute, and second representation of the input geometry in the latitude and longitude projection.

**geometry**

[General expression](../../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), its value must be valid ST_GEOMETRY type data, and the type must be POINT.

**format**

The format is a format string, limited to a length of 1024, used to represent the format of the return value.

The format string has the following four options:

- D: represents degrees
- M: represents minutes
- S: represents seconds
- C: represents cardinal direction

***Example*** for Heap tables

```enbf
format = 'D°M''S.SSS"C'.
```

This parameter must follow these rules:

* Must include D.
* If M is omitted, degrees will be displayed in decimal, and the precision of degrees will match the specified digits.
* If S is omitted, minutes will be displayed in decimal, and the precision of minutes will match the specified digits.
* If C is omitted, degrees will be displayed with a "-" sign when south or west.
* The D, M, S characters can be repeated within the options to specify the desired width and precision; the C character cannot be repeated.
* The D, M, S, and C options can each only appear once in the format string.
* If omitted (or zero length), the default format `D°M''S.SSS"C` will be used.

When any input parameters are NULL, the function returns NULL.

***Example*** for Heap tables

```sql
--Omitting format
SELECT ST_AsLatLonText(ST_GeomFromText('POINT(-58.2687 29.149)')) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
29°8''56.400"N 58°16''7.320"W                                

--Normalized coordinates
SELECT ST_AsLatLonText(ST_GeomFromText('POINT (1234 56789)')) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
89°0''0.000"S 26°0''0.000"W                                   

--Including format
SELECT ST_AsLatLonText(ST_GeomFromText('POINT(-58.2687 29.149)'), 'D degrees, M minutes, S seconds C') res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
29 degrees, 8 minutes, 56 seconds N 58 degrees, 16 minutes, 7 seconds W

--Error on repeated options
SELECT ST_ASLATLONTEXT(ST_GEOMFROMTEXT('POINT(-58.2687 29.149)'), 'D°M.MMMM''S"CD') res FROM DUAL;

YAS-07202 plugin execution error, bad format, cannot include degrees/minutes/seconds more than once

--NULL parameters
SELECT ST_AsLatLonText(ST_GeomFromText('POINT(-58.2687 29.149)'), NULL) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
   
SELECT ST_AsLatLonText(NULL, 'D degrees, M minutes, S seconds C') res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
   
```
