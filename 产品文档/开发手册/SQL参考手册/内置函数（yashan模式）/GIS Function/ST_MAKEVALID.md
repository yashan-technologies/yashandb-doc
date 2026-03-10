```ebnf+diagram
st_makevalid::= ST_MAKEVALID "(" geometry [ "," params ] ")"
```

ST_MAKEVALID函数将尝试在不丢失输入的任何顶点的前提下，为指定的无效几何体geometry创建有效表达，并返回相应的有效几何图形。

本函数遵守如下规则：
* 输入的几何体包含点、多点、线串、多线串、多边形、多多边形和包含它们任意组合的集合。
* 发生部分或完全纬度坍缩的情况下，函数将返回可能是低至相等纬度几何的集合，或一个较低维度几何。
* 在自相交的情况下，单个多边形可能会变成多几何体。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。		  
	

**params**

用于指定构建有效几何图形的方法，通过`key=value`的方式指定，多个键值对间使用空格进行分割，格式为: `method=linework|struct keepcollapsed=true|false`。
* method: 设置构建有效几何图形算法，支持linework和structure算法，默认值为linework。
  * inework：原始算法，先提取并连接所有线条，然后根据线框构建有效几何图形。
  * structure：区分内环和外环，通过合并外环构建新的几何形状，然后对所有内环进行差集操作。
* keepcollapsed: 指定是否删除集合中最低维度的几何组件，仅对structure算法有效。
  * 当设置为true时，不会删除折叠到集合中最低维度的几何组件。
  * 当设置为false时，会删除折叠到集合中最低维度的几何组件，例如一维线串（linestring）。
  



示例（HEAP表）

```sql
SELECT ST_AsText(ST_MakeValid(st_geomfromtext('POINT(1 2)')), 0) FROM dual;

ST_ASTEXT(ST_MAKEVALID(ST_GEOMFROMTEXT('POINT(12)')),0)          
---------------------------------------------------------------- 
POINT (1 2)                                                     

SELECT ST_AsText(ST_MakeValid(st_geomfromtext('LINESTRING(0 0, 0 0)'),'method=linework'), 0) FROM dual;

ST_ASTEXT(ST_MAKEVALID(ST_GEOMFROMTEXT('LINESTRING(00,00)'),'MET 
---------------------------------------------------------------- 
POINT (0 0)                                                 

SELECT ST_AsText(ST_MakeValid(st_geomfromtext('POINT(1 0)'), 'method=structure keepcollapsed=true'), 0) FROM dual;

ST_ASTEXT(ST_MAKEVALID(ST_GEOMFROMTEXT('POINT(10)'),'METHOD=STRU 
---------------------------------------------------------------- 
POINT (1 0)
```