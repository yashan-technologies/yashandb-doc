通用描述
----

SAVEPOINT用于在事务过程中建立一个保存点，在ROLLBACK语句中可以根据此保存点来指定回滚在它之后的操作。

如果事务被执行了COMMIT语句，则包括指定了保存点的回滚操作都将无效。

如果保存点的名称出现重复，旧的保存点将被覆盖而导致失效。

如果按某一保存点进行了回滚，其后面定义的保存点将被清除。

语句定义
----

**savepoint::=**

```ebnf+diagram
syntax::= SAVEPOINT name
```

### 1. savepoint\_name

该语句用于指定保存点的名称，不可省略，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

分布式部署中不允许创建名称以`SYS_S_`开头的保存点。

示例

```sql
-- 初始数据
SELECT area_no,area_name,DHQ FROM area WHERE area_no='01';
AREA_NO AREA_NAME                                                     DHQ                 
------- ------------------------------------------------------------- ---------------------
01      华东                                                        Shanghai  
  
-- 设置保存点
UPDATE area SET DHQ='Hangzhou' WHERE area_no='01';
SAVEPOINT sa_area_1;
UPDATE area SET DHQ='Nanjing' WHERE area_no='01';
SAVEPOINT sa_area_1;
UPDATE area SET DHQ='Suzhou' WHERE area_no='01';
SAVEPOINT sa_area_3;
  
-- 按保存点回滚,由于重名，第一个保存点无效
ROLLBACK TO SAVEPOINT sa_area_1;
SELECT area_no,area_name,DHQ FROM area WHERE area_no='01';
AREA_NO AREA_NAME                                                     DHQ                 
------- ------------------------------------------------------------- ---------------------
01      华东                                                        Nanjing       
  
-- 由于已执行了到前面保存点的回滚，sa_area_3被清除
ROLLBACK TO SAVEPOINT sa_area_3;
YAS-02022 savepoint sa_area_3 not exist
```
