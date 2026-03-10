DBMS_APPLICATION_INFO高级包用于注册应用程序的名称和操作，便于审核、性能跟踪以及资源使用情况观测。应用程序的名称和操作信息将记录在V$SESSION、V$SQL和V$SQLAREA视图中。


> **Note**:
>
> - DBMS_APPLICATION_INFO高级包仅适用于单机部署。
> - 通过调用DBMS_APPLICATION_INFO相关过程所设置的模块名（module_name）和操作名（action_name）不会因当前会话退出而删除，在任意会话中再次执行同一模块/操作时会直接复用对应的名称，您也可以再次调用相关过程设置新的名称。

#### READ\_CLIENT\_INFO

```plsql
DBMS_APPLICATION_INFO.READ_CLIENT_INFO (
   client_info OUT VARCHAR2); 
```

READ_CLIENT_INFO用于获取当前会话的client_info字段的值。此外，可以通过查询V$SESSION视图获取客户端信息。

| 参数        | 描述                                                    |
| :---------- | :------------------------------------------------------ |
| client_info | 截至目前最后一次调用SET_CLIENT_INFO所设置的客户端信息。 |

#### READ\_MODULE

```plsql
DBMS_APPLICATION_INFO.READ_MODULE ( 
   module_name OUT VARCHAR2, 
   action_name OUT VARCHAR2); 
```

READ_MODULE用于获取当前会话的模块和操作字段的值。此外，可以通过查询V$SQL视图或V$SQLAREA视图获取已注册的应用程序的模块和操作名称。

| 参数        | 描述                                                         |
| :---------- | :----------------------------------------------------------- |
| module_name | 截至目前最后一次调用SET_MODULE所设置的模块名称。             |
| action_name | 截至目前最后一次调用SET_ACTION或SET_MODULE所设置的操作名称。 |

#### SET\_MODULE

```plsql
DBMS_APPLICATION_INFO.SET_MODULE ( 
   module_name IN VARCHAR2, 
   action_name IN VARCHAR2); 
```

SET_MODULE用于设置当前模块及模块中操作的名称。

名称是模块/操作（实际动作）的描述性文本，建议在开始执行某个模块/操作前先调用此过程设置模块和操作的名称并在模块/操作执行完后再次调用此过程将名称更新为下一模块/操作的名称或置为NULL（若结束时不及时更新名称或置NULL会继续使用前一名称记录后续的模块/操作），从而保障名称（描述文本）与模块/操作（实际动作）的关联关系，正确记录事务更便于审核或性能跟踪。

| 参数        | 描述                                                         |
| :---------- | :----------------------------------------------------------- |
| module_name | 当前正在运行的模块的名称，长度为[1,48]字节，超出部分将被截断。允许为NULL，表示不设置模块名称。 |
| action_name | 当前模块中当前正执行的操作的名称，长度为[1,32]字节，超出部分将被截断。允许为NULL，表示不设置操作的名称。 |

#### SET\_ACTION

```plsql
DBMS_APPLICATION_INFO.SET_ACTION (
   action_name IN VARCHAR2); 
```

SET_ACTION用于设置当前模块中当前操作的名称。

| 参数        | 描述                                                         |
| :---------- | :----------------------------------------------------------- |
| action_name | 当前模块中当前正执行的操作的名称，长度为[1,32]字节，超出部分将被截断。允许为NULL，表示不设置操作的名称。 |

#### SET\_CLIENT\_INFO

```plsql
DBMS_APPLICATION_INFO.SET_CLIENT_INFO (
   client_info IN VARCHAR2); 
```

SET_CLIENT_INFO用于设置当前客户端信息的名称。

| 参数        | 描述                                                         |
| :---------- | :----------------------------------------------------------- |
| client_info | 当前会话的客户端信息的名称，长度为[1,64]字节，超出部分将被截断。允许为NULL，表示不设置客户端信息。 |


示例（单机部署）

```plsql
-- 设置客户端信息和模块信息
BEGIN
  DBMS_APPLICATION_INFO.SET_CLIENT_INFO('Web Application');
  DBMS_APPLICATION_INFO.SET_MODULE('Data Access', 'Querying Employees');
END;
/

-- 读取客户端信息和模块信息
DECLARE
  client_info VARCHAR2(64);
  module_name VARCHAR2(64);
  action_name VARCHAR2(64);
BEGIN
  DBMS_APPLICATION_INFO.READ_CLIENT_INFO(client_info);
  DBMS_OUTPUT.PUT_LINE('Client Info: ' || client_info);
  
  DBMS_APPLICATION_INFO.READ_MODULE(module_name, action_name);
  DBMS_OUTPUT.PUT_LINE('Module Name: ' || module_name);
  DBMS_OUTPUT.PUT_LINE('Action Name: ' || action_name);
END;
/
```
