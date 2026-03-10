The DBMS_PICKLER package provides a built-in function to query UDT metadata information.

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
The GET_TYPE_SHAPE function implements querying type information and its subtype information from base tables such as `SYS.USER$`.

Function parameter information:

|Parameter |Description |Type |IN/OUT |
| :-------------- |:--------------------------------------------------------------------------------------|:--------------|:-------|
| Return Value    | tdsFlag, indicates whether TDS data is sent through lob, non-0 indicates lob is sent.    | INT            | OUT    |
| FULLTYPENAME    | Full UDT name, Oracle takes it as an in parameter.                                         | VARCHAR        | IN/OUT |
| TYPOID          | UDT oid.                                                                                   | BIGINT         | OUT    |
| VERSION         | Type version number, auto-incremented after `alter type`, initial version is 1.          | INT            | OUT    |
| TDS             | `Type Descriptor Source`, contains `attr numbers`, n attribute `type codes`; if `tdsFlag` is 1, indicates this field is lob sent. | RAW            | OUT    |
| INSTANTIABLE    | Is instantiable, usually YES.                                                               | VARCHAR        | OUT    |
| SUPERTYPE_OWNER | Schema of the parent type.                                                                  | VARCHAR        | OUT    |
| SUPERTYPE_NAME  | Name of the parent type.                                                                    | VARCHAR        | OUT    |
| ATTR_RC         | Cursor for attribute metadata information.                                                  | SYS_REFCURSOR  | OUT    |
| SUBTYPE_RC      | Cursor for subtype metadata information.                                                    | SYS_REFCURSOR  | OUT    |

ATTR_RC attribute metadata information:

|Field |Explanation |
| :------------ | :---------------------------------------------------- |
| 1              | Fixed value, 1, `image format`.                          |
| NAME           | Attribute name.                                         |
| ATTRIBUTE      | Attribute sequence number, indicating which attribute of the type; starts from 1. |
| DTypeName      | Type name, such as VARCHAR2, NUMBER, NVARCHAR2, CLOB. |
| USER.NAME      | Name of the owner of the attribute type (if it's UDT), ordinary types are null. |
| ATTR_TOID      | `Type id` of the attribute, if the attribute type is also UDT. |
| ATTRTYPINS     | Whether the type is instantiable, usually YES.        |
| SUP_TYP_OWNER  | Schema of the parent type if the attribute type has a parent type. |
| SUP_TYP_NAME   | `Type name` of the parent type if the attribute type has a parent type. |

SUBTYPE_RC subtype metadata information:

|Field |Explanation |
| :--- | :-------------------------- |
| 1     | Fixed value, 1, `image format`. |
| NAME  | Name of the subtype owner.      |
| NAME  | Name of the subtype.            |
| OID   | `Type oid` of the subtype.      |

***Example***:

```sql
-- Create UDT type
CREATE OR replace TYPE SALES.UDT_VARRAY_TYP IS object(id INT, value VARCHAR(20));
/

-- Enable dbms_output
SET serveroutput ON;
-- Anonymous block
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
