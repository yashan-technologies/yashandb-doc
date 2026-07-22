## discovery ac

本命令可以根据用户指定的SQL文件或历史SQL来发现推荐的AC语句。

|  选项| 含义|
| ---------------- | ------------------------------------------------------------ |
| *-c, --cluster*  | YashanDB的集群名（必传参数）                                             |
| *--file*         | 指定SQL文件，与history二选一                                 |
| *--history*      | 使用记录的历史SQL，与file二选一                              |
| *--explain*      | 指定SQL文件检查SQL正确性                                     |
| *-s, --schema*   | 指定SQL文件的schema，默认为SYS                               |
| *-sp, --schema-password* | 指定schema的密码                                    |
| *-f, --force*    | 跳过explain参数的确认提示                                    |
| *-m, --mode*     | 指定AC发现模式，可选'NORMAL'和'BASE'分别为正常模式和基础模式 |
| *-o, --output*   | 指定输出路径，生成AC列表文件                                 |
| *--parallelism*  | explain和解析SQL的并行度，范围为1-16，默认为1                |
| *-u, --username*     | 指定数据库用户，不指定则默认使用sys用户           |
| *-p, --password*  | 数据库用户的密码<br/>若使用sys用户且已开启[操作系统认证](../../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)（安装后默认开启）则无需指定密码   |
| *-h,--help*        | 查看当前命令的帮助信息 |

示例

```shell
$ yasboot discovery ac -c yashandb --file ac.sql

$ yasboot discovery ac -c yashandb --history
```

**SQL文件规格**

当前单个SQL文件默认规格为：文件大小不超过200M，SQL数不超过100万条。

**ac.sql示例**

```sql
select a, b from yasom.test01 where c=2 and d=3;
select a from "yasom".test01 where b=3 and d=7;
```

**ac发现结果**

```text
+------------------------------------------------------------------------------------------------------------------------+
| Schema | Table  | Statement                                                                                            |
+------------------------------------------------------------------------------------------------------------------------+
| YASOM  | TEST01 | CREATE ACCESS CONSTRAINT YASOM.YASOM_TEST01_AC1 FROM YASOM.TEST01 ON C,D TO A,B;                     |
+--------+--------+------------------------------------------------------------------------------------------------------+
| yasom  | TEST01 | CREATE ACCESS CONSTRAINT "yasom".YASOM_TEST01_AC2 FROM "yasom".TEST01 ON B,D TO A;                   |
+--------+--------+------------------------------------------------------------------------------------------------------+
```
