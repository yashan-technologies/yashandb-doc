通用描述
----

COMMENT用于为表|视图或表|视图的列字段添加注释。可以在USER\_TAB\_COMMENTS/USER\_COL\_COMMENTS等系统视图中查询添加的注释信息。

不能对已创建的COMMENT进行删除/修改操作，每执行一次COMMENT会覆盖上一次的注释信息，如想删除注释信息，请将COMMENT的内容指定为''。

语句定义
----

**comment::=**

```ebnf
= COMMENT ON (TABLE [schema "."] (table|view)|COLUMN [schema "."] (table|view) "." column) IS string.
```

### TABLE table|view

该语句用于指定要添加注释信息的表|视图名称。

示例

```sql
COMMENT ON TABLE area IS '区域表';
SELECT table_name,table_type,comments FROM USER_TAB_COMMENTS WHERE table_name='AREA';
TABLE_NAME        TABLE_TYPE COMMENTS    
----------------- ---------- -------------
AREA              TABLE      区域表      
  
-- 清除注释
COMMENT ON TABLE area IS '';
SELECT table_name,table_type,comments FROM USER_TAB_COMMENTS WHERE table_name='AREA';
TABLE_NAME        TABLE_TYPE COMMENTS    
----------------- ---------- -------------
AREA              TABLE
```

### COLUMN table|view

该语句用于指定要添加注释信息的列字段的名称。

示例

```sql
COMMENT ON COLUMN area.DHQ IS '区域总部';
SELECT table_name,column_name,comments FROM USER_COL_COMMENTS WHERE table_name='AREA' AND column_name='DHQ';
TABLE_NAME        COLUMN_NAME       COMMENTS     
----------------- ----------------- --------------
AREA              DHQ               区域总部     
  
-- 清除注释
COMMENT ON COLUMN area.DHQ IS '';
SELECT table_name,column_name,comments FROM USER_COL_COMMENTS WHERE table_name='AREA' AND column_name='DHQ';             
TABLE_NAME        COLUMN_NAME       COMMENTS     
----------------- ----------------- --------------
AREA              DHQ
```
