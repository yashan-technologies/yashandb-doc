## 通用描述

ALTER SESSION用于动态地改变所在会话的属性或会话级的系统配置，此类改变仅在当前会话生效，会话断开后将恢复为初始值。

执行本语句，用户需具备ALTER SESSION权限。

## 语句定义

**alter session::=**

```ebnf+diagram
syntax::= ALTER SESSION set_clause
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

用户登录数据库后，当前Schema默认为登录用户的同名Schema，允许通过执行ALTER SESSION语句手动切换current_schema。如需确认当前会话的Schema（即current_schema的参数值）可以通过[DBMS_UTILITY](../../PL参考手册/内置高级包/DBMS_UTILITY).OLD_CURRENT_SCHEMA()查询。

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

parameter_name可以指定为[配置参数](../../../参考手册/配置参数)中的会话级参数。

示例（单机、共享集群部署）

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
