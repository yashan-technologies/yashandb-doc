## 卸载YashanDB客户端（Windows）

1. 在Windows配置用户环境变量的工具中，删除yashandb-client相关环境变量。

2. 删除客户端目录，例如D:\yashandb-client。

## 卸载YashanDB客户端（Linux）

<span id="ScriptUninstall" name="ScriptUninstall" ></span>

### 卸载通过脚本安装的YashanDB客户端

1. 删除~/.bashrc中的yashandb-client相关环境变量。

2. 删除客户端目录，如/home/yasdb/yashandb_client。

<span id="RPMUninstall" name="RPMUninstall" ></span>

### 卸载通过rpm安装的YashanDB客户端

1. 在使用YashanDB客户端的用户下删除~/.bashrc中的yashandb-client相关环境变量。

2. 在root用户下卸载rpm包。

   ```shell
   # rpm -q yashandb
   yashandb-client-{version_number}-1.el7.x86_64
   # rpm -e yashandb-client-{version_number}-1.el7.x86_64
   ```

3. 删除/usr/local/yashandb_client目录。
