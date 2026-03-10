The DBMS_LICENSE package provides a set of built-in stored procedures/functions to implement license-related functionality. 

This advanced package is not suitable for ISC Distributed Cluster Deployment.

## UPDATE\_LICENSE

```plsql
DBMS_LICENSE.UPDATE_LICENSE(
	license_file_path  VARCHAR);
```

UPDATE_LICENSE is used to update LICENSE information by obtaining and parsing the LICENSE file based on the specified file path. After determining its legitimacy, it updates the system table.

|Parameter |Description |
| :----- | :----------------------------------------------------------- |
| license_file_path  | The absolute path of the LICENSE file.            |

***Example*** for Standalone Deployment and YAC Deployment

```plsql

exec DBMS_LICENSE.UPDATE_LICENSE ('/home/yashan/license.lic');
```
