VALIDATE用于检查catalog中记录的备份集文件是否完整，从而确定是否可以用于恢复。


```ebnf+diagram
syntax::= VALIDATE BACKUPSET (TAG "tag_name" | "backupset_path")
```

可以指定备份集的TAG进行校验，检查该TAG是否存在于catalog中且本地可访问。

也可以直接指定备份集绝对路径，确认该路径是否本地可访问。

示例

```shell
$ yasrman -c "VALIDATE BACKUPSET tag 'full_1'" -D /home/yashan/catalog

$ yasrman -c "VALIDATE BACKUPSET '/home/yashan/full_1'" -D /home/yashan/catalog

$ yasrman -c "VALIDATE BACKUPSET '+DG0/bak1'" -D /home/yashan/catalog
```

> **Note**：
>
> 校验命令必须指定catalog路径。
>
> 校验命令无需与数据库建立连接，故无需指定IP信息。
>
> 若需要校验共享存储内的备份集文件，则在执行前需要导入环境变量YASCS_HOME和YASFS_HOME，否则无法访问共享存储文件。
