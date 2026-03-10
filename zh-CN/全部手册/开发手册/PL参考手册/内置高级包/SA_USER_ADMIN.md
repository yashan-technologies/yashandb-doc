SA_USER_ADMIN包提供了一组内置子程序，用于设置、移除用户与LBAC安全策略的关联关系。

- 该高级包不适用于存算一体分布式集群部署。

- 执行该高级包的用户需具备LBAC_DBA角色。

## SET\_USER\_LABELS

```plsql
SA_USER_ADMIN.SET_USER_LABELS (
    policy_name     IN VARCHAR,
    user_name       IN VARCHAR,
    max_read_label  IN VARCHAR,
    max_write_label IN VARCHAR DEFAULT NULL,
    min_write_label IN VARCHAR DEFAULT NULL,
    def_label       IN VARCHAR DEFAULT NULL,
    row_label       IN VARCHAR DEFAULT NULL);
```
此存储过程用于为用户关联LBAC安全标签，从而建立用户与LBAC安全策略的关联关系。

|  参数| 描述|
| :--- | :---- |
| policy_name| LBAC安全标签所属策略的名称 |
| user_name| 用户名 |
| max_read_label| 用户的最大读标签。设置用户可以被赋予的最大的读权限标签，包含最大可以设置的级别和范围组合 |
| max_write_label| 用户的最大写标签。设置用户可以被赋予的最大的写权限标签，包含最大可以设置的级别和范围组合。需满足如下规则：<br/>（1）max_write_label中的level等于max_read_label中的level。<br/>（2）max_write_label的范围内容为 max_read_label范围内容的子集。<br/>（3）若该参数为空，则值为max_read_label。<br/> |
| min_write_label| 用户的最小写标签。指定用户的最小写标签，只包含级别。若该参数为空，则值为该策略内的最小等级 |
| def_label| 默认会话标签，标签的范围内容是max_read_label范围内容的子集。若该参数为空，则值为max_read_label。 <br/>def_label内容，在视图DBA_SA_USER_LABELS中，以default_read标签、default_write标签展现。<br/> \* default_read 用户的读权限标签，标签中的等级值为def_label标签的等级制，标签中的范围内容是max_read_label的范围内容和def_label的范围内容的子集。<br/> \* default_write 用户的写权限标签，标签中的等级值为def_label标签的等级制，标签中的范围内容是max_write_label的范围内容和def_label的范围内容的子集 <br/> |
| row_label| 行标签，包含的等级的值不能大于def_label中的等级的值，包含的范围内容是def_label范围的子集 |

示例（单机/共享集群/分布式集群部署）

```plsql
BEGIN
    SA_USER_ADMIN.SET_USER_LABELS ('lbac_policy', 'sales', 'GENERAL:MNG');
END;
/

```

## DROP\_USER\_ACCESS

```plsql
SA_USER_ADMIN.DROP_USER_ACCESS (
    policy_name IN VARCHAR,
    user_name IN VARCHAR);
```
此存储过程用于解除用户与指定LBAC安全策略的关联关系。

|  参数| 描述|
| :--- | :---- |
| policy_name| LBAC安全策略 |
| user_name| 用户名 |

示例（单机/共享集群/分布式集群部署）

```plsql
BEGIN
    SA_USER_ADMIN.DROP_USER_ACCESS ('lbac_policy', 'sales');
END;
/

```
