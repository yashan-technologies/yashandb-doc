```ebnf+diagram
st_aslatlontext::= ST_ASLATLONTEXT "(" geometry "," format ")"
```

ST_ASLATLONTEXT函数根据输入的geometry，返回该geometry在经纬度投影中的度、分、秒表示形式。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据，且类型必须为POINT。

**format**

format是一个格式字符串，长度限制到1024，用于表示返回值的格式。

格式字符串有如下四个选项：

- D：代表度数
- M：代表分钟
- S：代表秒
- C：代表基本方向

示例（HEAP表）

```enbf
format = 'D°M''S.SSS"C'.
```

该参数需遵守如下规则：

*   必须包含D。
*   如省略M，则度数以十进制显示，且度数精度与指定的位数相同。
*   如省略S，则分钟将显示为十进制，且分钟精度与指定的位数相同。
*   如省略C，则度数在南或西时以“-”符号显示。
*   可以在各选项中通过重复使用D、M、S字符以指定所需的宽度和精度，C字符不可重复。
*   D、M、S和C选项在格式字符串中只能出现一次。
*   如省略（或零长度），将使用默认格式`D°M''S.SSS"C`。

当输入的参数存在NULL时，函数返回NULL。

示例（HEAP表）

```sql
--省略format
SELECT ST_AsLatLonText(ST_GeomFromText('POINT(-58.2687 29.149)')) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
29°8''56.400"N 58°16''7.320"W                                

--归一化坐标
SELECT ST_AsLatLonText(ST_GeomFromText('POINT (1234 56789)')) res FROM DUAL;

RES                                             
---------------------------------------------------------------- 
89°0''0.000"S 26°0''0.000"W                                   

--包含format
SELECT ST_AsLatLonText(ST_GeomFromText('POINT(-58.2687 29.149)'), 'D degrees, M minutes, S seconds C') res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
29 degrees, 8 minutes, 56 seconds N 58 degrees, 16 minutes, 7 seconds W

--选项重复使用时返回错误
SELECT ST_ASLATLONTEXT(ST_GEOMFROMTEXT('POINT(-58.2687 29.149)'), 'D°M.MMMM''S"CD') res FROM DUAL;

YAS-07202 plugin execution error, bad format, cannot include degrees/minutes/seconds more than once

--参数存在NULL
SELECT ST_AsLatLonText(ST_GeomFromText('POINT(-58.2687 29.149)'), NULL) res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
   
SELECT ST_AsLatLonText(NULL, 'D degrees, M minutes, S seconds C') res FROM DUAL;

RES                                                              
---------------------------------------------------------------- 
   
```

