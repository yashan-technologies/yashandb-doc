YashanDB中的ST_GEOMETRY类型是数据库内置的一种自定义类型，用于存储和访问符合开放地理空间信息联盟（Open Geospatial Consortium，简称OGC）制定的SFA SQL标准的几何对象。

## 存储属性

|  类型| 字节长度|
|--------|---------|
| ST_GEOMETRY   | 4GB |

## 定义格式

定义格式为`ST_GEOMETRY(SPATIAL TYPE,SRID)`或`ST_GEOMETRY(SPATIAL TYPE)`或`ST_GEOMETRY`，其中SPATIAL TYPE和SRID的含义如下：

*   SPATIAL TYPE：表示ST_GEOMETRY的空间类型，限制列中数据为特定子类型和维度。若子类型为GEOMETRY，列中的数据可以为任意子类型。SPATIAL TYPE通过后缀来指定坐标维度限制，当前只支持后缀Z作为三维坐标限制。若无后缀，限制数据维度为二维。
*   SRID：表示空间参考系标识号，用于限制列中数据的SRID，可省略，默认为0。参数值类型为INTEGER，SRID的值域同INTEGER类型值域，但SRID值小于0时视作0处理。
    * 指定SRID为0（或小于0）：表示不限制列中数据的SRID。
    * 指定SRID大于0：表示限制列中数据的SRID为指定值。若插入数据的SRID为0或与指定值相等，插入后数据的SRID将统一保存为SRID指定值，否则报错且数据插入失败。

示例（HEAP表）

```sql
--1.创建包含ST_GEOMETRY类型的表position，pos1不限制子类型、维度和SRID，pos2限制子类型为POINT并且只支持二维坐标，不限制SRID，pos3不限制子类型，限制维度为三维、SRID为4326
CREATE TABLE position (id INT, pos1 ST_GEOMETRY, pos2 ST_GEOMETRY(POINT), pos3 ST_GEOMETRY(GEOMETRYZ, 4326));

--2.向pos2插入子类型为LINESTRING的数据，因为不符合POINT子类型，所以会有Geometry type (LINESTRING) does not match column type (POINT)报错
INSERT INTO POSITION(pos2) VALUES(ST_GEOMFROMTEXT('LINESTRING(1 2, 2 2)'));
YAS-07202 plugin execution error, Geometry type (LINESTRING) does not match column type (POINT)

--3.向pos3插入子类型为POINT的二维数据，因为不包含Z坐标，所以会有Column has Z dimension but geometry does not报错
INSERT INTO POSITION(pos3) VALUES(ST_GEOMFROMTEXT('POINT(1 1)'));
YAS-07202 plugin execution error, Column has Z dimension but geometry does not

--4.向pos3插入SRID为3857的三维数据，因为不符合对列指定的SRID，所以会有Geometry SRID (3857) does not match column SRID (4326)报错
INSERT INTO POSITION(pos3) VALUES(ST_GEOMFROMTEXT('POINT(1 1 1)', 3857));
YAS-07202 plugin execution error, Geometry SRID (3857) does not match column SRID (4326)

--5.向pos3插入SRID为0的三维数据，因为指定了列的SRID为4326，所以会将数据的SRID转换为4326。
INSERT INTO POSITION(pos3) VALUES(ST_GEOMFROMTEXT('POINT(1 1 1)', 0));

SELECT ST_SRID(pos3) FROM position;
ST_SRID(POS3) 
------------- 
         4326
```

## ST\_GEOMETRY属性

|  **属性名称**| 类型| 长度|
|--------|------| -------------------------- |
| HEAD   | RAW | 40字节 |
| GEOM   | BLOB| < 4G |

## ST\_GEOMETRY支持的子类型

- POINT

  点代表坐标系空间中的单个坐标。

  ```sql
  POINT (1 2)
  POINT Z (1 2 3)
  ```

- LINESTRING

  线是由连续的线段组成，且一条线段由两点定义，点之间需要使用逗号隔开。

  ```sql
  LINESTRING (1 2,4 5)
  ```

- POLYGON

  多边形是由外环（闭合的LINESTRING）及0条或多条内环（闭环的LINESTRING）组成，外环与内环之间需要使用逗号隔开。

  ```sql
  POLYGON ((1 0,1 1,2 2,1 0),(0 0,6 6,8 8,0 0))
  ```

- MULTIPOINT

  表示点的集合，点之间需要使用逗号隔开。

  ```sql
  MULTIPOINT ((1 1),(2 2))
  ```

- MULTILINESTRING

  表示线的集合，线之间需要使用逗号隔开。

  ```sql
  MULTILINESTRING ((1 2,4 5),(2 3,5 6))
  ```

- MULTIPOLYGON

  表示多边形的集合，多边形之间需要使用逗号隔开。

  ```sql
  MULTIPOLYGON (((1 5, 4 3, 6 6, 2 6, 1 5)), ((6 5, 8 8, 6 9, 6 5)))
  ```

- GEOMETRYCOLLECTION

  GEOMETRYCOLLECTION可由不同的ST_GEOMETRY子类型组成一个集合，且子类型之间需要使用逗号隔开。

  ```sql
  GEOMETRYCOLLECTION (POINT (1 0),LINESTRING (1 2,4 5))
  ```

## 使用规则

### 使用限制

对于ST_GEOMETRY类型数据的使用限制如下：

- 仅适用于HEAP表。

- 在ST_GEOMETRY类型上只能创建RTree索引，不能创建其他类型索引。

- 不能作为分区表的分区键。

- 不能作为GROUP BY的分组键。

### 使用格式

ST_GEOMETRY类型可通过符合OGC规范的交换格式（如WKT、WKB）输入，也可以将其转换成WKT、WKB等交换格式。

#### WKT

WKT（Well-Known Text）格式为描述空间数据的标准格式，格式样例如下：

```sql
POINT(2 5)
LINESTRING((1 3),(4 5))
POLYGON ((1 0,1 1,2 2,1 0),(0 0,6 6,8 8,0 0))
```

#### WKB

WKB（Well-Known Binary）格式为描述空间数据的二进制格式，本格式与对应的WKT格式如下：

```sql
WKT:POINT(5 0)
WKB:010100000000000000000014400000000000000000

WKT:LINESTRING (1 2,4 5)
WKB:010200000002000000000000000000F03F000000000000004000000000000010400000000000001440
```

示例（HEAP表）

```sql
--1.创建包含ST_GEOMETRY类型的表position_wkb
CREATE TABLE position_wkb (id INT, pos ST_GEOMETRY);

--2.通过WKT格式插入ST_GEOMETRY对象
INSERT INTO position_wkb 
VALUES (1, ST_GEOMFROMTEXT('POINT(0 0)'));
COMMIT;

--3.通过WKB格式插入ST_GEOMETRY对象
INSERT INTO position_wkb
VALUES (2, ST_GEOMFROMWKB('0101000000000000000000F03F000000000000F03F'));
COMMIT;

--4.以WKT格式查询ST_GEOMETRY对象
SELECT id, ST_ASTEXT(pos) FROM position_wkb;

ID           ST_ASTEXT(POS)
------------ ----------------------------------------------------------------
           1 POINT (0.000000000000000 0.000000000000000)
           2 POINT (1.000000000000000 1.000000000000000)
```

## ST\_GEOMETRY的特性

**坐标**

每个坐标都有一个X和Y坐标值，以确定其在平面中的位置。形状由点或线段构造，其中点由单个坐标指定，线段由两个坐标指定。

坐标可以包含可选的Z和M坐标值。Z常被用来表示高度。M是一个测量值，可以表示时间或距离。如果Z或M值出现在ST_GEOMETRY值中，则必须为ST_GEOMETRY值的每个点定义它们。如果ST_GEOMETRY类型数据有Z或M坐标，则坐标维度为3D；如果它既有Z又有M，那么坐标维度就是4D。

**坐标参考系**

对ST_GEOMETRY类型数据的所有空间操作都使用该数据所在的坐标参考系的单位。坐标参考系由SRID编号标识。X轴和Y轴的单位由坐标参照系确定。在平面参考系统中，X和Y坐标通常代表东方和北方，而在大地测量系统中，它们代表经度和纬度。SRID=0表示一个无限的笛卡尔平面，它的轴上没有单位。

**维度**

维度是ST_GEOMETRY类型的属性。点类型的维度为0，线性类型的维度为1，多边形类型的维度为2。集合的维度为最大元素维度。

YashanDB目前最高仅支持到三维坐标，如输入四维坐标则会忽略第四个坐标。

**外包框**

包含ST_GEOMETRY坐标的二维或三维方框，它是表示ST_GEOMETRY类型数据在坐标空间中的范围以及检验两个ST_GEOMETRY类型数据是否相互作用的一种有效方法。
