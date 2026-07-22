DBMS_SESSION包提供了一组内置的存储过程，用于管理会话上下文信息，会话信息。

高级包不适用于存算一体分布式集群部署。

## SET_IDENTIFIER

```plsql
DBMS_SESSION.SET_IDENTIFIER (
	client_identifier   IN   VARCHAR);
```

SET_IDENTIFIER为存储过程，通过传入client_identifer，为用户会话设置客户端标识符，设置后V$SESSION上展示对应字段CLIENT_IDENTIFIER。

|  参数| 描述|
| :------------------ | :--------|
|client_identifier    |用户传入用于设置客户端标识符的字符串，最大长度为64字节，输入超过64字节报错|

示例（单机部署）

```plsql
BEGIN
  DBMS_SESSION.SET_IDENTIFIER('abcd');
END;
/
```

## CLEAR_IDENTIFIER

``` plsql
DBMS_SESSION.CLEAR_IDENTIFIER;
```

CLEAR_IDENTIFIER为存储过程，用于清除用户设置的标识符。

无参数。

示例（单机部署）

```plsql
BEGIN
    DBMS_SESSION.CLEAR_IDENTIFIER;
END;
/
```

## FREE_UNUSED_USER_MEMORY

``` plsql
DBMS_SESSION.FREE_UNUSED_USER_MEMORY;
```

FREE_UNUSED_USER_MEMORY为存储过程，仅用于兼容，对系统无实际影响。

无参数。

示例（单机部署）

```plsql
BEGIN
    DBMS_SESSION.FREE_UNUSED_USER_MEMORY;
END;
/
```

## SET_CONTEXT

```plsql
DBMS_SESSION.SET_CONTEXT (
   namespace VARCHAR(64),
   attribute VARCHAR(128),
   value     VARCHAR(4000),
   username  VARCHAR(128),
   client_id VARCHAR(64) );
```

此存储过程用于设置应用程序上下文信息，存储过程只能在CONTEXT对象关联包内部执行。

基于会话程序上下文，username，client_id 不生效。 

|  参数| 描述|
| :--------| :--------------------------------- |
| namespace| 上下文名称                         |
| attribute| 上下文属性名称                     |
| value    | 上下文值                           |
| username | 上下文信息对应用户名称，默认值NULL |
| client_id| 上下文信息对应客户端ID，默认值NULL |

示例（单机/共享集群/分布式集群部署）

```plsql
-- 创建context对象
CREATE CONTEXT ctx1 USING set_context_pkg;

-- 创建context对象关联package
CREATE OR REPLACE PACKAGE set_context_pkg IS 
   PROCEDURE set_attr(name in varchar2,attr in varchar2, value in varchar2);
END; 
/

CREATE OR REPLACE PACKAGE BODY set_context_pkg IS
   PROCEDURE set_attr(name in varchar2,attr in varchar2, value in varchar2) 
   IS 
   BEGIN
    DBMS_SESSION.SET_CONTEXT(name, attr, value);
   END;
 END;
/

-- 执行set context存储过程
exec set_context_pkg.set_attr('ctx1', 'attr1', 'aaa');
```

## CLEAR_CONTEXT

```plsql
DBMS_SESSION.CLEAR_CONTEXT(
    namespace  VARCHAR(64),
    client_identifier VARCHAR(64), 
    attribute  VARCHAR(128));
```

此存储过程用于清除设置上下文属性对应信息，存储过程只能在CONTEXT对象关联包内部执行。

- 基于会话程序上下文，client_id 不生效。
- 如果attribute参数为空，则清除namespace上下文所有信息。  

|  参数| 描述|
| :---------------- | :--------------------------------- |
| namespace         | 上下文名称                         |
| client_identifier | 上下文信息对应客户端ID，默认值NULL |
| attribute         | 上下文属性名称                     | 

示例（单机/共享集群/分布式集群部署）

```plsql
-- 创建context对象
CREATE CONTEXT ctx1 USING set_context_pkg;

-- 创建context对象关联package
CREATE OR REPLACE PACKAGE set_context_pkg IS 
   PROCEDURE clear_attr(name in varchar2, attr in varchar2);
END; 
/

CREATE OR REPLACE PACKAGE BODY set_context_pkg IS
   PROCEDURE clear_attr(name in varchar2,attr in varchar2) 
   IS 
   BEGIN
    DBMS_SESSION.CLEAR_CONTEXT(name, attr);
   END;
 END;
/

-- 执行clear context存储过程
exec set_context_pkg.clear_attr('ctx1', 'attr1');
```

## CLEAR_ALL_CONTEXT 

```plsql
DBMS_SESSION.CLEAR_ALL_CONTEXT(namespace  VARCHAR(64));
```

此存储过程用于清除上下文对应名称所有信息，存储过程只能在CONTEXT对象关联包内部执行。

|  参数| 描述|
| :--------- | :----------- |
| namespace  | 上下文名称   |

示例（单机/共享集群/分布式集群部署）

```plsql
-- 创建context对象
CREATE CONTEXT ctx1 USING set_context_pkg;

-- 创建context对象关联package
CREATE OR REPLACE PACKAGE set_context_pkg IS 
   PROCEDURE clear_all_attr(name in varchar2);
END; 
/

CREATE OR REPLACE PACKAGE BODY set_context_pkg IS
   PROCEDURE clear_all_attr(name in varchar2) 
   IS 
   BEGIN
    DBMS_SESSION.CLEAR_ALL_CONTEXT(name);
   END;
 END;
/

-- 执行clear all context存储过程
exec set_context_pkg.clear_all_attr('ctx1');
```
<span id="closedblink" name="closedblink"></span>

## CLOSE_DATABASE_LINK

``` plsql
DBMS_SESSION.CLOSE_DATABASE_LINK(
   dblink_name varchar
   );
```

此存储过程用于关闭当前会话中的远程数据库链接。

|  参数| 描述|
| :--------- | :----------- |
| dblink_name  | dblink_name需指定为已开启的远程数据库链接的名称，且需确保该链接相关的事务已提交或回滚。可通过[V$DBLINK](../../../参考手册/系统视图/动态视图/V$DBLINK.md)视图获取当前会话的已开启的DBLink及其事务状态。
   |

示例（单机/共享集群/分布式集群部署）

```plsql
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

-- 执行存储过程CLOSE_DATABASE_LINK，关闭目标DBLink
exec DBMS_SESSION.CLOSE_DATABASE_LINK(LinkToOra);

-- 确认关闭结果
SELECT DB_LINK,IN_TRANSACTION FROM V$DBLINK WHERE DB_LINK = 'LinkToOra';

DB_LINK                                                          IN_TRANSACTION
---------------------------------------------------------------- --------------
```

## RESET_PACKAGE

```plsql
DBMS_SESSION.RESET_PACKAGE;
```

此过程会销毁当前会话中所有包的实例，并释放包的状态。

无参数。

示例（单机/共享集群/分布式集群部署）

```plsql
BEGIN
 DBMS_SESSION.RESET_PACKAGE();
END;
/
```
