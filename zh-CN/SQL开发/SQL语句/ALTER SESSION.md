## 通用描述

ALTER SESSION用于动态地改变所在会话的属性或会话级的系统配置，此类改变仅在当前会话生效，会话断开后将恢复为初始值。

执行本语句，用户需具备ALTER SESSION权限。

## 语句定义

**alter session::=**

```ebnf+diagram
syntax::= ALTER SESSION (set_clause|CLOSE DATABASE LINK dblink_name)
```

**set_clause::=**

```ebnf+diagram
syntax::= SET parameter_name "=" parameter_value
```

### 1. set\_clause

该语句用于指定会话属性或会话级系统配置参数的值。

#### 1.1. 会话属性参数

**current_schema**

该参数表示所在会话的当前Schema属性。

用户登录数据库后，当前Schema默认为登录用户的同名Schema，允许通过执行ALTER SESSION语句手动切换current_schema。如需确认当前会话的Schema（即current_schema的参数值）可以通过[DBMS_UTILITY](../../全部手册/开发手册/PL参考手册/内置高级包/DBMS_UTILITY).OLD_CURRENT_SCHEMA()查询。

用户执行SQL语句操作模式对象时，如果没有显式指定对象所属Schema，系统会默认视为操作current_schema下的对象。

> **Note**: 
>
> 权限校验基于用户，切换当前schema不影响当前所登录用户权限。

示例

```sql
-- 系统中存在sales1、sales2两个用户
-- 以sales1用户登录开启会话（当前schema为sales1）
conn sales1/1%2;

-- 创建area表（area表属于sales1）
DROP TABLE IF EXISTS area;
CREATE TABLE area
(area_no CHAR(2) NOT NULL,
 area_name VARCHAR2(60),
 DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL);
INSERT INTO area VALUES ('01','EastChina','Shanghai');
INSERT INTO area VALUES ('02','WestChina','Chengdu');
COMMIT;

SELECT area_no,area_name,DHQ FROM area;
AREA_NO AREA_NAME                  DHQ                   
------- -------------------------- -------------------
01      EastChina                       Shanghai             
02      WestChina                       Chengdu      

-- 修改current_schema为sales2
ALTER SESSION SET current_schema=sales2;

-- 查询修改结果
SELECT DBMS_UTILITY.OLD_CURRENT_SCHEMA() current_schema FROM dual;
CURRENT_SCHEMA
----------------------------------------------------------------
SALES2

-- 不显式指定schema访问area表，默认为sales2.area，而sales2下并不存在area表
SELECT area_no,area_name,DHQ FROM area;
[1:15]YAS-02012 table or view does not exist
```

#### 1.2. 会话级系统配置参数

parameter_name可以指定为[配置参数](../../全部手册/参考手册/配置参数)中的会话级参数。

示例（单机/共享集群/分布式集群部署）

```sql
-- 会话级参数date_format
SHOW PARAMETER date_format;
NAME                  VALUE   
--------------------- ------- 
DATE_FORMAT			  yyyy-mm-dd     
 
ALTER SESSION SET date_format='yyyy-mm-dd hh24:mi:ss';

SHOW PARAMETER date_format;
NAME                  VALUE   
--------------------- ------- 
DATE_FORMAT			  yyyy-mm-dd hh24:mi:ss 

-- 会话级参数ISOLATION_LEVEL
-- 设置会话的默认隔离级别为SERIALIZABLE。
ALTER SESSION SET ISOLATION_LEVEL = SERIALIZABLE;

-- 设置会话的默认隔离级别为READ COMMITTED。
ALTER SESSION SET ISOLATION_LEVEL = READ COMMITTED;
```

<span id="closedblink" name="closedblink" class="yaslink"></span>

### 2. CLOSE DATABASE LINK

该语句用于关闭当前会话中的远程数据库链接。


若在会话中使用过DBLink，DBLink将始终保持打开状态直至结束当前会话或手动关闭DBLink。DBLink可能会申请系统空闲内存来缓存远端表业务数据，如确认暂时无需再次使用某个DBLink，可使用该语句显式关闭目标DBLink释放相应资源。


dblink_name需指定为已开启的远程数据库链接的名称，且需确保该链接相关的事务已提交或回滚。可通过[V$DBLINK](../../全部手册/参考手册/系统视图/动态视图/V$DBLINK)视图获取当前会话的已开启的DBLink及其事务状态。


示例（单机/共享集群/分布式集群部署）

```sql
-- 查询当前会话中已开启的DBLink以及是否正处于事务中
SELECT DB_LINK,IN_TRANSACTION FROM V$DBLINK;

DB_LINK                                                          IN_TRANSACTION
---------------------------------------------------------------- --------------
LinkToOra                                                        YES

-- IN_TRANSACTION=YES表示正处于事务中，按需提交或回滚事务后才能关闭DBLink
COMMIT;

-- 确认目标DBLink的事务状态
SELECT DB_LINK,IN_TRANSACTION FROM V$DBLINK WHERE DB_LINK = 'LinkToOra';

DB_LINK                                                          IN_TRANSACTION
---------------------------------------------------------------- --------------
LinkToOra                                                        NO

-- 关闭目标DBLink
ALTER SESSION CLOSE DATABASE LINK LinkToOra;

-- 确认关闭结果
SELECT DB_LINK,IN_TRANSACTION FROM V$DBLINK WHERE DB_LINK = 'LinkToOra';

DB_LINK                                                          IN_TRANSACTION
---------------------------------------------------------------- --------------
```
