## Functionality Overview

The yacConnect function is used to initiate a connection request from the client. It returns YAC_SUCCESS to indicate a successful connection and YAC_ERROR to indicate a connection failure.

## Function Declaration

```c
yacResult yacConnect ( yacHandle hConn,
                       const yacChar* url,
                      YacInt16 urlLength,
                      const yacChar* user,
                      YacInt16 userLength,
                      const yacChar* password,
                      YacInt16 passwordLength);
```

## Parameter Description

|Parameter Name |Description |
| ------------------- |---------------------------------------------------------|
| hConn (IN/OUT)      | Connection information handle.                  |
| url (IN)            | The URL of the target database, supporting single IP, multiple IPs, or multiple IP groups.<br/>Configuration format is as follows:<br>* Single IP: `host:port`<br>* Multiple IPs: `serverType:host:port,host:port;host:port,host:port`, multiple addresses are separated by `,`, connections will poll to corresponding nodes based on serverType configuration.<br>* Multiple IP groups: `serverType:host:port,host:port;host:port,host:port`, multiple IP groups are separated by `;`, when connecting, it will first poll the corresponding nodes within the group according to the serverType configuration. If all connections within the group fail, it will access the next group in order of priority (the higher the position, the higher the priority).<br>Parameter meanings:<br>* host:port: The database's listening address.<br>* serverType: Connection mode, optional options include [primary&#124;standby&#124;loadBalance&#124;primaryLoadBalance&#124;standbyLoadBalance]. For detailed descriptions, please refer to the text below. |
| urlLength (IN)      | Length of the server address; you can pass YAC_NULL_TERM_STR to indicate a null-terminated string.                     |
| user (IN)           | Username.                                  |
| userLength (IN)     | Length of the username; you can pass YAC_NULL_TERM_STR to indicate a null-terminated string.             |
| password (IN)       | User password.              |
| passwordLength (IN) | Length of the user password; you can pass YAC_NULL_TERM_STR to indicate a null-terminated string.           |

Detailed description of the serverType parameter in the listening address is as follows:

|Parameter Name |Description |
| ------------------- |---------------------------------------------------------|
| primary             | Indicates polling connection to the primary database in a high-availability deployment with multiple IPs (does not connect to the standby database). This is the default mode for multiple IP/multiple IP group scenarios and can be omitted. |
| standby             | Indicates polling connection to the standby database in a high-availability deployment with multiple IPs (does not connect to the primary database). |
| loadBalance         | Indicates load-balanced connection to the optimal node (i.e., the node with the fewest connections) in a setting with multiple IPs. |
| primaryLoadBalance  | Indicates load-balanced connection to the optimal primary database (the primary database with the fewest connections) in a setting with multiple IPs. |
| standbyLoadBalance  | Indicates load-balanced connection to the optimal standby database (the standby database with the fewest connections) in a setting with multiple IPs. |