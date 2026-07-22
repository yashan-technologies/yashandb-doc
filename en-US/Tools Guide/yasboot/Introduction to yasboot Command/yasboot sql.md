*yasboot* integrates the functionality related to the *yasql* tool, which is used to open the *yasql* client or execute SQL directly from the command line.

|Option |Meaning |
| ---------------- | ------------------------------------------------------------ |
| *-c,--cluster*    | Cluster name of YashanDB                                   |
| *-n, --node-id*   | Node ID in the deployed cluster, for example 1-1          |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |
| *-d, --dsn*       | Provides a way to input username and password as a string, for example 'username/password@127.0.0.1:1688' |
| *-e, --echo*      | Output the execution process of the SQL file to the console |
| *-s, --sql*       | Execute a single SQL command and exit                     |
| *-f, --file*      | Support importing and executing SQL files                  |
| *-h,--help*          | View help information for the current command  |

***Example***1 for connecting to the yasql client

```shell
# Connect using node ID
$ yasboot sql -n 1-1 -u username -p password -c yashandb

# Connect using IP:PORT
$ yasboot sql -d username/password@127.0.0.1:1688

# IPv6 requires square brackets
$ yasboot sql -d username/password@[::1]:1688
```

***Example***2 for executing SQL statements

```shell
$ yasboot sql -n 1-1 -u username -p password -c yashandb -s 'select status from v$instance;'

STATUS            
----------------- 
OPEN             

1 row fetched.
```

***Example***3 for executing SQL files

```shell
$ yasboot sql -d username/password@127.0.0.1:1678 -f get_status.sql --echo
```
