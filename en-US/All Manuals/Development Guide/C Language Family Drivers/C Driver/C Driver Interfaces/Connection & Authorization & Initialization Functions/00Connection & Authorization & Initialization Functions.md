## Connection String Description

|Parameter |Description |
| ------- | ------------------------------------------------------------ |
| gSrvStr   | Database connection descriptor, format is HOST:PORT.<br />* HOST: The server domain name or IP address, must be configured to the address of a standalone instance server or a distributed CN server.<br />* PORT: The database service port, such as 1688. |
| user      | Database username.                                           |
| pwd       | Database user password.                                     |

## Interface

|Interface Name |Interface Description |
|--------------------|--------------------------------------|
| [yacCancel](yacCancel)   | Client initiates a request to cancel the current session. |
| [yacConnect](yacConnect) | Client initiates a request to connect.               |
| [yacDisconnect](yacDisconnect) | Client initiates a request to disconnect.              |