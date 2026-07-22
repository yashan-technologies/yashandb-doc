## dataspace redistribute

This command is used for manually redistributing data in a distributed DN node group.

|Option |Meaning |
| ---------------- |-------------------------------|
| *-c, --cluster*     | The cluster name of YashanDB (required parameter) |
| *--dataspace-id*    | Dataspace id (DS_ID in route$), default is 0 |
| *--target-group*     | The ID of the target group (e.g., `1`. You can view the database information through the `yasboot cluster status` command and take the number before the hyphen of `nodeid`) |
| *--chunk-id*        | ChunkId (CHUNK in route$)             |
| *--clean-residual-immediately* | Whether to automatically clean up the chunk table space |
| *--disable*         | Suppress progress information          |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |
| *-d, --child*       | Display execution information of sub-tasks |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot dataspace redistribute -c yashandb
$ yasboot dataspace redistribute -c yashandb --chunk-id 1 --target-group 4
```
