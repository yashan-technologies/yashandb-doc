自治事务是由PL中由另一个事务（主事务）启动的独立事务。自治事务不与主事务共享锁，资源或提交依赖关系，可独立执行SQL操作并进行提交或回滚。

自治事务相关限制如下：

- 不允许在自治事务中执行LSC表冷数据的DML操作。

## 声明自治事务

声明自治事务可以在程序开头使用如下命令：PRAGMA AUTONOMOUS\_TRANSACTION。声明后即开始AUTONOMOUS ROUTINE，其中可包含多个COMMIT以及ROLLBACK。

声明自治事务遵循以下规则：

- 自治事务必须在最外层的DECLARE中的声明。
- 自治事务不是一个嵌套的事务，其提交跟回滚操作不影响主事务。
- 一个自治例程可包含多个自治事务。
- 例程中与事务相关的语句最后必须显式地使用COMMIT或ROLLBACK，否则会导致报错并将语句内容回滚。
- 自治事务执行异常或没有COMMIT/ROLLBACK的场景下会将异常进行上抛，当外层有相应的EXCEPTION时可以对异常进行处理。
- 匿名块，过程体，触发器以及函数可以声明为自治事务，其余类型不可使用。

示例

```plsql
create or replace procedure Autonomous_Insert
as
   pragma autonomous_transaction;
begin
    insert into area values ( '00','autonomous','Autonomous Insert' );
    insert into area values ( '99','autonomous','Autonomous Insert' );
    commit;
end;
/

begin
    insert into area values ( '98','autonomous','Commit Block' );
    Autonomous_Insert;
    rollback;
end;
/

select area_no,area_name,DHQ from area;

--result

AREA_NO AREA_NAME                                                     DHQ
------- ------------------------------------------------------------- ---------------------
01      EastChina                                                          Shanghai
02      WestChina                                                          Chengdu
03      SouthChina                                                          Guangzhou
04      NorthChina                                                          Beijing
05      CentralChina                                                          Wuhan
00      autonomous                                                      Autonomous Insert
99      autonomous                                                      Autonomous Insert

7 rows fetched.

DELETE FROM area WHERE area_name='autonomous';

create or replace procedure Autonomous_Insert
as
   pragma autonomous_transaction;
begin
    insert into area values ( '00','autonomous','Autonomous Insert' );
end;
/

begin
    insert into area values ( '97','autonomous','Rollback Block' );
    Autonomous_Insert;
    rollback;
end;
/

YAS-05232 active autonomous transaction detected and rolled back
```
