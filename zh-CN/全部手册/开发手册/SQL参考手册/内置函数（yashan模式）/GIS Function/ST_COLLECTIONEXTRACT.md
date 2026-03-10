```ebnf+diagram
st_collectionextract::= ST_CollectionExtract "(" geometry ["," type ] ")"
```

ST_COLLECTIONEXTRACT函数用于从指定几何集合中获取同类型的多几何体。

**geometry**

[通用表达式](../../通用SQL语法/expr)，其值必须为有效的ST_GEOMETRY类型的数据。

**type**

用于指定返回值的几何体类型，可省略，可选项包括1、2和3，分别表示点POINT、线串LINSTRING和多边形POLYGON。

* 不指定类型时，函数返回包含最高维度几何图形的多几何体，维度级别：多边形 > 线 > 点。
* 指定类型时，函数返回包含指定类型的多几何体。若geometry中不存在指定type的元素则返回EMPTY。

示例（HEAP表）

```sql
SELECT ST_AsText(ST_CollectionExtract(st_geomfromtext('POINT(16 16)'), 1), 0) FROM dual;

ST_ASTEXT(ST_COLLECTIONEXTRACT(ST_GEOMFROMTEXT('POINT(1616)'),1) 
---------------------------------------------------------------- 
POINT (16 16)                                                     

SELECT ST_AsText(ST_CollectionExtract(st_geomfromtext('MULTILINESTRING Z ((0 0 0, 2 0 0), (2.09887763434 3.91 0, 2 2 0))'), 2), 0) FROM dual;

ST_ASTEXT(ST_COLLECTIONEXTRACT(ST_GEOMFROMTEXT('MULTILINESTRINGZ 
---------------------------------------------------------------- 
MULTILINESTRING Z ((0 0 0, 2 0 0), (2 4 0, 2 2 0))                                                

SELECT ST_AsText(ST_CollectionExtract(st_geomfromtext('GEOMETRYCOLLECTION Z (MULTIPOLYGON Z (((0 0 5,10 0 5,10 10 5,0 10 5,0 0 5),(2 2 5,2 5 5,5 5 5,5 2 5,2 2 5))),POINT Z (0 0 5),MULTILINESTRING Z ((0 0 5, 2 0 5),(1 1 5, 2 2 5)))')), 0) FROM dual;

ST_ASTEXT(ST_COLLECTIONEXTRACT(ST_GEOMFROMTEXT('GEOMETRYCOLLECTI 
---------------------------------------------------------------- 
MULTIPOLYGON Z (((0 0 5, 10 0 5, 10 10 5, 0 10 5, 0 0 5), (2 2 5, 2 5 5, 5 5 5, 5 2 5, 2 2 5)))
```
