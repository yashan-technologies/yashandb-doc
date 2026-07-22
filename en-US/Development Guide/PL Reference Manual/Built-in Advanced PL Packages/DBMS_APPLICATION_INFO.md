The DBMS_APPLICATION_INFO advanced package is used to register the name and operation of applications, facilitating auditing, performance tracking, and resource usage monitoring. The application name and operation information are recorded in the V$SESSION, V$SQL, and V$SQLAREA views.

> **Note**:
>
> The module name (module_name) and action name (action_name) set by calling related procedures of DBMS_APPLICATION_INFO will not be removed when the current session exits. Re-executing the same module/action in any session will directly reuse the corresponding name, and you can also call related procedures again to set new names.

#### READ_CLIENT_INFO

```plsql
DBMS_APPLICATION_INFO.READ_CLIENT_INFO (
   client_info OUT VARCHAR2); 
```

READ_CLIENT_INFO is used to retrieve the value of the client_info field for the current session. In addition, client information can be obtained by querying the V$SESSION view.

|Parameter |Description |
| :---------- | :------------------------------------------------------ |
| client_info  | The client information set during the last call to SET_CLIENT_INFO. |

#### READ_MODULE

```plsql
DBMS_APPLICATION_INFO.READ_MODULE ( 
   module_name OUT VARCHAR2, 
   action_name OUT VARCHAR2); 
```

READ_MODULE is used to retrieve the module and action field values for the current session. Additionally, registered application module and action names can be obtained by querying the V$SQL or V$SQLAREA views.

|Parameter |Description |
| :---------- | :----------------------------------------------------------- |
| module_name  | The module name set during the last call to SET_MODULE.  |
| action_name  | The action name set during the last call to SET_ACTION or SET_MODULE. |

#### SET_MODULE

```plsql
DBMS_APPLICATION_INFO.SET_MODULE ( 
   module_name IN VARCHAR2, 
   action_name IN VARCHAR2); 
```

SET_MODULE is used to set the name of the current module and actions within the module.

The name serves as descriptive text for the module/action (actual operation). It is recommended to call this procedure to set the module and action names before starting to execute a certain module/action and to call the procedure again after completing the module/action to update the name for the next module/action or set it to NULL (if the name is not updated timely or set to NULL at the end, the previous name will continue to be used for subsequent modules/actions), thereby ensuring the association between the name (descriptive text) and module/action (actual operation) is correctly recorded for auditing or performance tracking purposes.

|Parameter |Description |
| :---------- | :----------------------------------------------------------- |
| module_name  | The name of the currently running module, with a length of [1,48] bytes, excess part will be truncated. NULL is allowed, indicating no module name is set. |
| action_name  | The name of the currently executing operation within the module, with a length of [1,32] bytes, excess part will be truncated. NULL is allowed, indicating no action name is set. |

#### SET_ACTION

```plsql
DBMS_APPLICATION_INFO.SET_ACTION (
   action_name IN VARCHAR2); 
```

SET_ACTION is used to set the name of the current action within the current module.

|Parameter |Description |
| :---------- | :----------------------------------------------------------- |
| action_name  | The name of the currently executing action within the module, with a length of [1,32] bytes, excess part will be truncated. NULL is allowed, indicating no action name is set. |

#### SET_CLIENT_INFO

```plsql
DBMS_APPLICATION_INFO.SET_CLIENT_INFO (
   client_info IN VARCHAR2); 
```

SET_CLIENT_INFO is used to set the name of the current client information.

|Parameter |Description |
| :---------- | :----------------------------------------------------------- |
| client_info  | The name of the client information for the current session, with a length of [1,64] bytes, excess part will be truncated. NULL is allowed, indicating no client information is set. |

***Example*** for Standalone Deployment

```plsql
-- Set client information and module information
BEGIN
  DBMS_APPLICATION_INFO.SET_CLIENT_INFO('Web Application');
  DBMS_APPLICATION_INFO.SET_MODULE('Data Access', 'Querying Employees');
END;
/

-- Read client information and module information
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
