DBMS_LICENSE包提供了一组内置的存储过程/函数，用于实现许可证相关功能。

该高级包不适用于存算一体分布式集群部署。

## UPDATE\_LICENSE

```plsql
DBMS_LICENSE.UPDATE_LICENSE(
	license_file_path  VARCHAR);
```

UPDATE_LICENSE用于更新LICENSE信息，根据指定的文件路径获取并解析LICENSE文件，确定其合法性后更新至系统表中。

|  参数| 描述|
| :----- | :----------------------------------------------------------- |
| license_file_path | LICENSE 文件的绝对路径。 |

示例（单机/共享集群/分布式集群部署）

```plsql

exec DBMS_LICENSE.UPDATE_LICENSE ('/home/yashan/license.lic');
```
