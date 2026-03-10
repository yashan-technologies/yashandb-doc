YashanDB单机、共享集群部署中表对象的默认存储方式为HEAP方式，数据按行（Row）组织，系统根据每一行数据所在物理地址信息生成一个全局唯一记录，即ROWID。ROWID用于检索表数据时的寻址，也可以用作每一行数据的唯一标识。

ROWID是一种特殊的数据类型，既能作为数据类型被用户进行列字段声明，也能作为伪列（非表结构中定义的列，但可以被当作列字段进行查询）被使用。

UROWID（Universal ROWID）即通用ROWID，在YashanDB中作为数据类型的一种，用于以二进制串形式存储行表的ROWID。

存储属性
----

| **类型** | 字节长度               |
| -------- | ---------------------- |
| ROWID    | 16                     |
| UROWID   | 1~8000，默认长度为4000 |

定义格式：

| **类型** | 格式         | 规则                                       |
| -------- | ------------ | ------------------------------------------ |
| ROWID    | ROWID        | 见下文数据格式                             |
| UROWID   | UROWID(Size) | 变长二进制串，若不指定Size则默认长度为4000 |

Size：宽度，表示最大字节长度。

数据格式
----

ROWID的数据格式为`dataoid:spaceid:fileid:blockid:dir`。

在用于存储行表的ROWID时，UROWID的数据格式为`0x1+ROWID的十六进制表述`。

### dataoid

data object id，行所在的Segment的ID，该值可从USER_OBJECTS等视图中的DATA_OBJECT_ID字段查询获得。

### spaceid

space id，行所在的表空间的ID，该值可从V$TABLESPACE等视图中的ID字段查询获得。

### fileid

file id，行所在数据文件在对应表空间中的数据文件ID，该值可从V$DATAFILE等视图中的ID字段查询获得。

### blockid

block id，行所在数据块在对应文件中的块ID。

### dir

dir，行在数据块上的槽位。

ROWID使用规则
----

ROWID可以作为伪列字段来使用，但必须被显式指定，即通过SELECT \*语句无法查询到ROWID的值。

ROWID也可以作为字段的数据类型，用来存储ROWID类型数据，且支持对其进行增删改查等DML操作。

在DML操作中，YashanDB支持ROWID与UROWID/RAW/字符型的隐式数据类型转换。但将ROWID作为函数的参数，且与函数要求的参数数据类型不一致时，函数不会对其进行数据类型的隐式转换，而是提示错误返回。

对于ROWID类型数据的使用限制如下：

- 不能作为分区键
- 仅适用于HEAP表，分布式HEAP表不支持ROWID。

对于ROWID伪列字段的使用限制如下：

- 不能在包含DISTINCT\GROUPBY\CONNECTBY\JOIN算子、ROWNUM伪列字段或聚合函数的子查询语句中使用ROWID伪列。

示例（HEAP表）

```sql
-- 查询ROWID伪列
SELECT ROWID,* FROM area WHERE area_no='01';
ROWID              AREA_NO AREA_NAME    DHQ     
------------------ ------- ------------ ---------
1350:5:0:148:0     01      华东         Shanghai
 
SELECT LENGTH(ROWID) FROM area WHERE area_no='01';
        LENGTH(ROWID)
---------------------
                   14
 
SELECT ISNULL(ROWID) FROM area WHERE area_no='01';
[1:7]YAS-04401 data type STRING expected, but ROWID got

SELECT ROWID FROM (SELECT area_no FROM area GROUP BY area_no);
YAS-04827 cannot select ROWID from, or sample, a view with DISTINCT, GROUP BY, etc.
    
-- 创建ROWID类型的列
CREATE TABLE ROWID_BASE(ID INT);

BEGIN
FOR I IN 1..10 LOOP
  INSERT INTO ROWID_BASE VALUES(I);
END LOOP;
COMMIT;
END;
/

CREATE TABLE ROWID_TEST(C1 ROWID);
INSERT INTO ROWID_TEST SELECT ROWID FROM ROWID_BASE;

--以字符串格式插入ROWID值
INSERT INTO ROWID_TEST VALUES ('1350:5:0:148:0');
```

UROWID使用规则
----

YashanDB内部将UROWID等同于[RAW](./RAW)类型，因此UROWID使用规则与RAW使用规则完全一致。

示例（HEAP表）

```sql
-- 将上例中ROWID_TEST表的ROWID插入到UROWID_TABLE表中的C1列，C1列类型为UROWID
CREATE TABLE UROWID_TABLE(C1 UROWID);
INSERT INTO UROWID_TABLE SELECT ROWID FROM ROWID_TEST;

--以字符串格式插入UROWID值，只要满足十六进制表述均会成功
INSERT INTO UROWID_TABLE VALUES ('1234ABCD');
--将其转为ROWID时，必须满足ROWID的数据格式要求，否则转换失败
INSERT INTO ROWID_TEST 
SELECT c1 FROM UROWID_TABLE
WHERE c1 = '1234ABCD';
YAS-00008 type convert error : invalid ROWID

SELECT ISNULL(c1) FROM UROWID_TABLE
WHERE c1='0171070000000000000061000009000500' AND ROWNUM=1;
ISNULL(C1)           
-------------------- 
false 
```

