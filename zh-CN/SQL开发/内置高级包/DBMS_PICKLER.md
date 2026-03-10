DBMS_PICKLER包提供了一个内置的函数，用于查询UDT元数据信息。

## GET\_TYPE\_SHAPE

```sql
DBMS_PICKLER.GET_TYPE_SHAPE (
    FULLTYPENAME     IN  VAECHAR,
    TYPOID           OUT BIGINT,
    VERSION          OUT INT,
    TDS              OUT RAW,
    INSTANTIABLE     OUT VARCHAR,
    SUPERTYPE_OWNER  OUT VARCHAR,
    SUPERTYPE_NAME   OUT VARCHAR,
    ATTR_RC          OUT SYS_REFCURSOR,
    SUBTYPE_RC       OUT SYS_REFCURSOR
) return INT;
```
GET_TYPE_SHAPE函数实现了从`SYS.USER$`等基础表中查询类型信息及其子类型信息。

函数参数信息：

|  参数| 描述| 类型| IN/OUT|
| :-------------- |:--------------------------------------------------------------------------------------|:--------------|:-------|
| 返回值          | tdsFlag，标识TDS数据是否通过LOB发送，非0表示LOB发送。                                                   | INT           | OUT    |
| FULLTYPENAME    | 自定义类型全名称，oracle将其作为in参数。                                                              | VARCHAR       | IN/OUT |
| TYPOID          | 自定义类型 oid。                                                                            | BIGINT        | OUT    |
| VERSION         | 类型版本号， `alter type` 后自增，初始版本为1。                                                       | INT           | OUT    |
| TDS             | `Type Descriptor Source`，包含 `attr numbers`，n个属性`type code`；如果`tdsFlag`为1，标识该字段是LOB发送。 | RAW           | OUT    |
| INSTANTIABLE    | 是否可实例化，通常为YES。                                                                        | VARCHAR       | OUT    |
| SUPERTYPE_OWNER | 父类型所属schema。                                                                          | VARCHAR       | OUT    |
| SUPERTYPE_NAME  | 父类型名称。                                                                                | VARCHAR       | OUT    |
| ATTR_RC         | 属性元数据信息 cursor。                                                                       | SYS_REFCURSOR | OUT    |
| SUBTYPE_RC      | 子类型元数据信息cursor。                                                                       | SYS_REFCURSOR | OUT    |

ATTR_RC属性元数据信息：

|  字段| 说明|
| :------------ | :---------------------------------------------------- |
| 1             | 固定值，1，`image format`。                           |
| NAME          | 属性名称。                                            |
| ATTRIBUTE     | 属性序号，表示类型的第几个属性；从1开始。             |
| DTypeName     | 类型名称，例如VARCHAR2，NUMBER，NVARCHAR2，CLOB。     |
| USER.NAME     | 属性类型（是自定义类型）owner的名称，普通类型为null。 |
| ATTR_TOID     | 属性的`type id`，如果属性类型也是自定义类型。         |
| ATTRTYPINS    | 类型是否可实例化，通常都是YES。                       |
| SUP_TYP_OWNER | 父类型的schema，如果属性类型有父类型。                |
| SUP_TYP_NAME  | 父类型的`type name`，如果属性类型有父类型。           |

SUBTYPE_RC子类型元数据信息：

|  字段| 说明|
| :--- | :-------------------------- |
| 1    | 固定值，1，`image format`。 |
| NAME | 子类型owner的名称。         |
| NAME | 子类型名称。                |
| OID  | 子类型的`type oid`。        |

示例：

```sql
-- 创建UDT类型
CREATE OR replace TYPE SALES.UDT_VARRAY_TYP IS object(id INT, value VARCHAR(20));
/

-- 启用dbms_output
SET serveroutput ON;
-- 匿名块
DECLARE
   tds_flag INT;
   full_type_name VARCHAR(255) := 'SALES.UDT_VARRAY_TYP';
   typeoid BIGINT;
   version INT;
   tds RAW(8000);
   instantiable VARCHAR(255);
   sup_owner VARCHAR(255);
   sup_name VARCHAR(255);
   attr_rc SYS_REFCURSOR;
   subtype_rc SYS_REFCURSOR;
BEGIN
   tds_flag := sys.dbms_pickler.get_type_shape(full_type_name, typeoid, version, tds, instantiable, sup_owner, sup_name, attr_rc, subtype_rc);
   dbms_output.put_line('tds flag: ' || tds_flag);
   dbms_output.put_line(' full_type_name: ' || full_type_name);
   dbms_output.put_line(' typeoid: ' || typeoid);
   dbms_output.put_line(' version: ' || version);
   dbms_output.put_line(' tds: ' || tds);
   dbms_output.put_line(' instantiable: ' || instantiable);
   dbms_output.put_line(' sup_owner: ' || sup_owner);
   dbms_output.put_line(' sup_name: ' || sup_name);
   DBMS_SQL.RETURN_RESULT(attr_rc);
   DBMS_SQL.RETURN_RESULT(subtype_rc);
END;
/

-- result
tds flag: 0
 full_type_name: SALES.UDT_VARRAY_TYP
 typeoid: 65581
 version: 1
 tds: 1C0000000000000001020004000100000000001A0002000000000000
 instantiable: YES
 sup_owner:
 sup_name:


ResultSet #1

1 NAME                                                               ATTRIBUTE# DECODE(A.ATTR_TOID,0                                             DECODE(BITAND(A.PROP
ATTR_TOID ATTRTYPINS SUP_TYP_OWNER                                                    SUP_TYP_NAME
------------ ---------------------------------------------------------------- ------------ ---------------------------------------------------------------- ---------------------------------------------------------------- --------------------- ---------- ---------------------------------------------------------------- ----------------------------------------------------------------
1 ID                                                                          1 INTEGER
4 YES
1 VALUE                                                                       2 VARCHAR
26 YES


ResultSet #2

1 NAME                                                             NAME                                                                              TOID
------------ ---------------------------------------------------------------- ---------------------------------------------------------------- ---------------------

```
