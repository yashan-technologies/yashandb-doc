*yaslicense* is the license generation tool used internally by YashanDB.

## license gen

This command generates the corresponding license file based on the esn information of the database server.

|Option |Meaning |
| ------------------ | ---------------------------------------------------- |
| *-h, --help*         | View help information                                |
| *-e, --esn*          | esn information of the database server (required parameter), can be obtained by executing the command [yasboot cluster esn gen](yasboot/Introduction to yasboot Command/yasboot cluster) on the server where the database is located  |
| *-v, --version*      | License version, default is v1                       |
| *-d, --deploy-type*  | Database deployment type<br>\* SE: Standalone Deployment, default value<br>\* CE: YAC/Distributed Cluster Deployment <br>\* DE: ISC Distributed Cluster Deployment                                    |
| *--expired-days*     | Expiration time (unit: days), default is 90         |
| *--cluster-version*  | Database version, default is 23.2                    |
| *--export-path*      | Export path, default exports to the current directory when the command is executed |

```shell
$ yaslicense license gen -e 10F6FD3D4D1DAD04F9E2791BA458F2827D9D67745242A0B9DB6B106DE123A130
```
