```ebnf+diagram
userenv::= USERENV "(" "'" parameter "'" ")"
```

The USERENV function returns session-related information based on the input parameter.

**parameter**

This is a string literal representing the parameter, case insensitive. It includes the following values:

*   SID: The SID of the current session, which uniquely identifies a connected session. The function returns a NUMBER type value for this parameter.
*   SCHEMAID: The current USER ID. The function returns a NUMBER type value for this parameter.
*   LANGUAGE: The character set used by the server for the current connection. The function returns a VARCHAR type value for this parameter.
*   TERMINAL: The identifier for the device or terminal where the current session is located. The function returns a VARCHAR type value for this parameter.
*   CLIENT_INFO: The user name logged into the current session and the path of the current session program. The function returns a VARCHAR type value for this parameter.
*   GSID: The global session ID for the current session in a distributed environment, where each session has a different GSID that is also different across different CNs. In a non-distributed environment, it returns the default value SID. The function returns a NUMBER type value for this parameter.
*   CURRENT_SCHEMAID: The current USER ID. The function returns a VARCHAR type value for this parameter.
*   INSTANCE: The current instance ID. The function returns a NUMBER type value for this parameter.
*   SESSIONID: The audit session ID for the current session.
*   GROUP_ID: The ID of the node group where the current node is located in ISC Distributed Cluster Deployment mode, displayed as 0 in Standalone/YAC/Distributed Cluster Deployment mode.
*   GROUP_NODE_ID: The ID of the node within the node group of the current node in ISC Distributed Cluster Deployment mode, displayed as 0 in Standalone/YAC/Distributed Cluster Deployment mode.

If the parameter is specified as any value other than those listed, the function returns an invalid parameter error.

***Example***

```sql
SELECT USERENV('CLIENT_INFO') res FROM DUAL;
RES                                            
----------------------------------------------------------------
user: SYS
program path: /home/yasdb/bin/yasql
```
