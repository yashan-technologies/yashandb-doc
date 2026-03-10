## yasboot init

This command provides an interactive quick deployment method for standalone databases.

|Option |Meaning |
| ------------------- | ------------------------------------------------------------ |
| --mode              | Installation mode, options are [local-host, multi-host]     |
| --ip                | The IP address for deployment, only supports IPv4 addresses, supports formats like `192.168.1.2,192.168.1.3` or `192.168.1.[2-4]` |
| -u, --username      | SSH username for the server                                   |
| -p, --password      | SSH login password for the server                             |
| -N,--no-password    | SSH passwordless login                                        |
| --ssh-port          | SSH connection port for the server                            |
| -c, --cluster       | Cluster name                                                 |
| --node              | Size of the standalone database node                     |
| --plugins           | Required plugin packages, options are [all,s3,gis,dblink,udf,listagg,none], default is all, multiple selections are supported, separated by commas |
| --install-path      | Installation path, generated YASDB_HOME will be \<install-path\>/\<version\> |
| --data-path         | Data path, generated YASDB_DATA will be \<data-path\>/db-\<nodeid\> |
| --log-path          | Path for yasagent, yasom, database run.log, slow.log        |
| --listen-on         | Address that the yasdb process listens on, options are [manage-ip, 0.0.0.0] |
| -sp, --sys-password  | Set the password for the database super administrator sys user, configuration requirements are as follows:<br/>* Password length must be 8 - 64 characters<br/>* Password must not contain the corresponding database username<br/>* Password must contain numbers, letters, and special characters<br/>* Special characters related to OS commands (such as `@`, `/`, `.`, `!`, `$`, `'`, etc.) must be escaped |
| --begin-port        | Starting port                                                |
| --memory-limit      | Maximum percentage of usable memory on the server, 0 means unlimited |
| --config-mode       | Configuration mode, options are [config-only,install-now]  |
| --fail-opt          | Operation after failure, options are [save,clean]           |
| --env-opt           | Method of adding environment variables after successful deployment, options are [automatic,manual] |
| --monit-opt         | Method of opening monit after successful deployment, options are [automatic,manual] |
| -y, --yes           | Select yes for all prompts [y/n]                            |
| --deps              | Local path of dependency package file (hidden parameter)     |
| -t, --yas-type      | Deployment type of the database: options are [SE, CE]       |
| --ce-data           | YAC data disks, supports multiple inputs, separated by commas |
| --disk-found-path   | YAC disk discovery paths, supports multiple inputs, separated by commas |
| --system-data       | YAC system data disks, supports multiple inputs, separated by commas |
| --group             | Number of YAC groups                               |
| --standby-node      | Deployment scale of standby nodes for YAC type    |
| -fg,--failgroup     | Number of failure groups in YAC disk group    |
| --yfs-force-create  | Force creation of diskgroup in yfs (only applicable for YACs) |
| --ignore-hostname   | Ignore server name (hidden parameter)                        |

> **Note**:
>
> When specifying --ignore-hostname, *yasboot* will ignore the server name and directly generate a default string (yas1, yas2, etc.) as the cluster node name for deployment. **Not recommended**, as using server names as cluster node names is more manageable.

***Example***

```shell
# Recommended: Execute the following command for interactive deployment
$ ./bin/yasboot init 

# Command line, ignoring failure checks may lead to deployment failures
$ ./bin/yasboot init --mode single-host --ip 127.0.0.1 -u yashan -p password --ssh-port 22 -c yashandb --node 1 --plugins all --install-path /data/yashan/yasdb_home --data-path /data/yashan/yasdb_data --log-path /data/yashan/log --listen-on manage-ip --sys-password password --begin-port 1688 --memory-limit 80 -y --yas-type SE
```
