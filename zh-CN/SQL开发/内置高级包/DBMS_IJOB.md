DBMS_IJOB包提供了一组内置的存储过程，用于对指定用户创建和管理[定时任务](../../全部手册/开发手册/PL参考手册/PL对象/定时任务)。需要注意的是，调用这些存储过程所执行的对定时任务的所有操作，均只有在执行COMMIT后才会生效。

该高级包不适用于存算一体分布式集群部署。

## SUBMIT

```plsql
DBMS_IJOB.SUBMIT(
    JOB OUT BINARY_INTEGER
	LUSER IN VARCHAR2,
	PUSER IN VARCHAR2,
    CUSER IN VARCHAR2 DEFAULT CURUSER,
    NEXT_DATE IN DATE DEFAULT SYSDATE,
    INTERVAL IN VARCHAR DEFAULT NULL,
    BROKEN IN BOOLEAN DEFAULT FALSE,
    WHAT IN VARCHAR2 NOT NULL
	CS_LAB IN VARCHAR2,
	CS_HI IN VARCHAR2,
	CS_LO IN VARCHAR2,
	NLSENV IN VARCHAR2,
	ENV IN VARCHAR2,
);
```

SUBMIT程序可以给指定的用户创建一个新的定时任务，成功创建的定时任务将可以在DBA_JOBS/ALL_JOBS/USER_JOBS视图中查询。

> **Note**: 
>
> 本程序只能被如下用户调用执行：
>
> - SYS用户
> - DBA角色的用户
> - 拥有all privileges权限的用户

|  参数| 描述|
| :-------- | :----------------------------------------------------------- |
| JOB       | 创建的任务编号                                               |
| LUSER     | 选填，不生效                                                 |
| PUSER     | 选填，不生效                                                 |
| CUSER     | 任务执行的角色，默认获取当前登录用户                         |
| NEXT_DATE | 定时任务下次执行的时间，默认立即执行                         |
| INTERVAL  | 表达式文本，用于计算定时任务下次执行的时间，表达式为NULL表示定时任务只执行一次。通过表达式计算出的时间必须为将来时间或者NULL |
| BROKEN    | 任务是否中断，默认FLASE                                      |
| WHAT      | 定时任务要执行的PL文本，可以是匿名块或者存储过程，必须以分号结束 |
| CS_LAB    | 选填，不生效                                                 |
| CS_HI     | 选填，不生效                                                 |
| CS_LO     | 选填，不生效                                                 |
| NLSENV    | 选填，不生效                                                 |
| ENV       | 选填，不生效                                                 |

示例（单机、共享集群部署）
```plsql
-- 在SALES用户下创建存储过程
CREATE TABLE tbl_job(id VARCHAR2(30), name VARCHAR2(30));
CREATE OR REPLACE PROCEDURE proce_t IS
BEGIN
INSERT INTO tbl_job(id, name) 
VALUES('1', TO_CHAR(SYSDATE, 'yyyy-mm-dd hh24:mi:ss'));
COMMIT;
END proce_t;
/

-- 以SYS用户执行，为SALES用户创建JOB
DECLARE
    job_id INT;
BEGIN
    SYS.DBMS_IJOB.SUBMIT(
    job_id,
 cuser=>'SALES',
 INTERVAL=> 'sysdate+1/24/60',
 what=> 'proce_t;');
COMMIT;
END;
/

-- 在SALES用户下查看job运行结果：每分钟插入一条数据
SELECT ID,NAME FROM tbl_job;
ID                                NAME                              
--------------------------------- --------------------------------- 
1                                 2022-11-08 19:50:57              
1                                 2022-11-08 19:51:57              
1                                 2022-11-08 19:52:57              
1                                 2022-11-08 19:53:57        

-- 在SALES用户下删除JOB（从user_jobs视图获取该JOB的ID）
exec DBMS_JOB.REMOVE(1925);
COMMIT;
```
