## catalog简介

yasrman使用catalog保存参数和备份集元信息，catalog为一个文件夹，包含如下内容：

- backup：默认备份集目录，用于存放指定存储到工具侧（DEST CLIENT）的备份集。当备份时未指定绝对路径时，工具端备份的备份集默认存放在该目录。

- catalog.meta：元数据文件，用于存放备份集位置、TAG以及分布式节点等相关元信息，为二进制文件。

- config.ini：配置文件，用于存放于备份恢复的语句配置信息。

- catalog.dwf：内部管理控制文件。

初次使用yasrman时，必须先创建catalog。

## 创建catalog

CREATE CATALOG用于创建一个新的catalog路径。

```ebnf+diagram
syntax::= CREATE CATALOG
```

本命令将根据-D选项指定的路径和名称创建一个catalog文件夹，未指定时在当前路径下创建一个名称为catalog的文件夹。若文件夹已存在，本命令报错。

> **Note**: 
>
> - catalog文件包含记录备份集元数据信息的二进制文件，不可手动删除。若删除或更换catalog路径等，可能会导致无法使用yasrman恢复其包含的备份集、无法查看备份集信息等异常情况。
> - catalog不绑定某个具体的数据库，但建议为不同的数据库创建独立的catalog文件。
> - yasrman不对catalog进行备份和恢复，用户可以在yasrman非工作期间手动拷贝catalog目录进行备份。
> - catalog备份集元数据信息与数据库系统表元数据信息不会自动同步。若发生过catalog迁移、catalog重建等操作，可能会出现[YAS-02505](../../../参考手册/错误码)备份指定tag已存在的报错（即与数据库系统表元数据信息中的TAG冲突），如需清理冲突TAG可查阅[删除备份集](DELETE)。

示例

```shell
$ yasrman sys/********@192.168.1.2:1688 -c 'create catalog' -D /home/yashan/catalog
```
