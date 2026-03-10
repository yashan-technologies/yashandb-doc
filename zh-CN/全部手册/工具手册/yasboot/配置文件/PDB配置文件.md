PDB配置文件列示在容器数据库环境中新增PDB的配置信息。

使用yasboot部署YashanDB为容器数据库时，将在$YASDB_HOME目录生成该文件。


如需为新建PDB规划自定义数据文件路径，在创建PDB前必须先完成对应路径的创建（以及权限配置），并在创建PDB时使用file-name-convert完成路径转换。

| 部署形态| 默认PDB_DATA| 自定义PDB_DATA要求|
| ---------------------------------- | --------------------------------- | ------------------------------------------------------------ |
| 单机部署                           | $YASDB_DATA/containers/{pdb_name} | 必须为本地路径，且数据库安装用户必须具备读写权限             |
| 共享集群/分布式集群部署            | +DG0/containers/{pdb_name}        | 必须为[YFS](../../../数据库管理/存储管理/集群文件系统管理/00集群文件系统管理)路径 |

> **Note**:
>
> 若PDB创建失败后再次重试时仍需使用原计划的自定义PDB_DATA，重试前还需确保目标路径为空。



示例：（单机部署pdb_add.toml）

```toml
file-name-convert="'?/containers/PDB$SEED/dbfiles','?/pdbs/pdb1/dbfiles','?/containers/PDB$SEED/local_fs','?/pdbs/pdb1/local_fs'"       # PDB的数据文件路径转换规则                
user="sys_pdb1"                           # PDB本地用户的名称
password="sys_pdb1"                       # PDB本地用户的密码
default-table-space-file="userspdb1"      # PDB内置的USERS表空间的数据文件
default-table-space-size="128M"           # PDB内置的USERS表空间容量
isarchive="false"                         # PDB是否开启归档模式。若创建CDB时已开启归档模式，该参数不生效，PDB的归档模式固定开启；若创建CDB时未开启归档模式，此处可自定义配置
```


示例：（共享集群/分布式集群部署pdb_add.toml）

```toml
file-name-convert="'+DG0/containers/PDB$SEED/dbfiles','+DG1/pdb1/dbfiles'"       # PDB的数据文件路径转换规则，必须指定为YFS路径                
user="sys_pdb1"                           # PDB本地用户的名称
password="sys_pdb1"                       # PDB本地用户的密码
default-table-space-file="userspdb1"      # PDB内置的USERS表空间的数据文件
default-table-space-size="128M"           # PDB内置的USERS表空间容量
isarchive="false"                         # PDB是否开启归档模式。若创建CDB时已开启归档模式，该参数不生效，PDB的归档模式固定开启；若创建CDB时未开启归档模式，此处可自定义配置
```
