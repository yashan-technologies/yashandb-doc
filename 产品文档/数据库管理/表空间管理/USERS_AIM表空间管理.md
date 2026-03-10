USERS_AIM表空间是分布式内置的memory mapped表空间，memory mapped类表空间包含文件的所有页面在系统运行时都映射在内存中。

USERS_AIM表空间只用于存储复制表，分布表则存储在[表空间集](../表空间集管理/00表空间集管理)中。

**为USERS_AIM表空间增加数据文件**

示例（分布式部署）

```sql
ALTER TABLESPACE users_aim ADD DATAFILE '?/dbfiles/users01' SIZE 16M;
```

**为USERS_AIM表空间删除数据文件**

示例（分布式部署）

```sql
ALTER TABLESPACE users_aim DROP DATAFILE '?/dbfiles/users01';
```

>  **Note**：
>
>  在上述语句中指定数据文件路径时，只允许按相对路径指定。

**查看使用USERS_AIM表空间的对象信息**

示例（分布式部署）

```sql
SELECT D.TABLESPACE_NAME, D.SEGMENT_NAME, D.SEGMENT_TYPE,SUM(BYTES)/1024/1024  SIZE_M FROM DBA_SEGMENTS D 
WHERE D.TABLESPACE_NAME = 'USERS_AIM'
GROUP BY D.TABLESPACE_NAME, D.SEGMENT_NAME, D.SEGMENT_TYPE ORDER BY SIZE_M DESC;

TABLESPACE_NAME         SEGMENT_NAME         SEGMENT_TYPE           SIZE_M 
----------------------- -------------------- ----------------- ----------- 
USERS_AIM               AREA0                TABLE                  2.4375		
```