## 安装YashanDB客户端

YashanDB客户端在Linux系统中的安装方法分为：

- [脚本安装](#ScriptInstall)：适用于所有的Linux平台，对应的卸载方法为[卸载通过脚本安装的YashanDB客户端](../../卸载/卸载YashanDB客户端.md#ScriptUninstall)。

- [rpm安装](#RPMInstall)：仅适用于CentOS Linux平台，对应的卸载方法为[卸载通过rpm安装的YashanDB客户端](../../卸载/卸载YashanDB客户端.md#RPMUninstall)。

<span id="ScriptInstall" name="ScriptInstall" ></span>

### 脚本安装

1. 从[YashanDB官网下载中心](https://download.yashandb.com/download)，或者联系我们的技术支持获取对应的软件包。

2. 将yashandb-client-{version_number}-linux-x86_64.tar.gz上传至本地路径（以/home/yasdb/yashandb_client为例），并解压。

   ```shell
   $ cd /home/yasdb/yashandb_client
   
   $ tar -zxf yashandb-client-{version_number}-linux-x86_64.tar.gz
   ```

   查看解压后内容。

   ```shell
   $ ll
   drwxrwxr-x. 2 yasdb yasdb      19 Feb  8 00:01 bin
   drwxrwxr-x. 2 yasdb yasdb      21 Feb  8 00:01 include
   drwxrwxr-x. 2 yasdb yasdb    4096 Feb  8 00:01 lib
   ```

3. 在~/.bashrc中配置PATH和LD_LIBRARY_PATH。

   ```shell
   $ vi ~/.bashrc
   export PATH=$PATH:/home/yasdb/yashandb_client/bin
   export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/yasdb/yashandb_client/lib
   ```

4. 生效环境变量。

   ```shell
   $ source ~/.bashrc
   ```

<span id="RPMInstall" name="RPMInstall" ></span>

### rpm安装

rpm安装方式仅适用于CentOS Linux平台，且必须以root用户执行安装。

1. 从[YashanDB官网下载中心](https://download.yashandb.com/download)，或者联系我们的技术支持获取对应的软件包。以yashandb-client-{version_number}-1.el7.x86_64.rpm为例。

2. 以root用户登录服务器，并将软件包上传到服务器（以/usr/local/install路径为例）。

3. 运行rpm。

   ```shell
   # cd /usr/local/install
   
   # rpm -ivh yashandb-client-{version_number}-1.el7.x86_64.rpm
   ```

4. rpm默认将客户端程序安装在/usr/local/yashandb_client路径，查看该目录下内容。

   ```shell
   # cd /usr/local/yashandb_client
   
   # ll
   drwxrwxr-x. 2 root root      19 Feb 10 02:49 bin
   drwxrwxr-x. 2 root root      21 Feb 10 02:49 include
   drwxrwxr-x. 2 root root    4096 Feb 10 02:49 lib
   ```

5. 切换到需使用YashanDB客户端的用户。

6. 在~/.bashrc中配置PATH和LD_LIBRARY_PATH。

   ```shell
   $ vi ~/.bashrc
   export PATH=$PATH:/usr/local/yashandb_client/bin
   export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/yashandb_client/lib
   ```

7. 生效环境变量。

   ```shell
   # source ~/.bashrc
   ```



## 后续操作 

- 字符集配置：Linux客户端的字符集默认为UTF8，如需调整请查阅[修改客户端字符集](../../../数据库管理/实例管理/数据库字符集配置.md#Client)。

- 可信信道：数据库客户端<->服务端可信信道默认关闭，如需进行加密通信请查阅[可信信道](../../../产品安全/加密支持/可信信道/00可信信道.md)。


