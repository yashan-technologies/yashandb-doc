## 环境要求

YashanDB客户端支持Apple Silicon架构的macOS平台。

## 安装YashanDB客户端

在macOS平台安装YashanDB客户端是通过脚本安装，其对应的卸载方法为[卸载通过脚本安装的YashanDB客户端](../../卸载/卸载YashanDB客户端.md#ScriptUninstall)。

1. 从[YashanDB官网下载中心](https://download.yashandb.com/download)，或者联系我们的技术支持获取对应的软件包。

2. 将yashandb-client-{version_number}-darwin-arm64.tar.gz上传至本地路径（以/Users/yashandb/yashandb_client为例），并解压。

   ```shell
   $ cd /Users/yashandb/yashandb_client
   
   $ tar -zxf yashandb-client-{version_number}-darwin-arm64.tar.gz
   ```

   查看解压后内容。

   ```shell
   $ ll
   drwxrwxr-x. 2 yasdb yasdb      19 Feb  8 00:01 bin
   drwxrwxr-x. 2 yasdb yasdb      21 Feb  8 00:01 include
   drwxrwxr-x. 2 yasdb yasdb    4096 Feb  8 00:01 lib
   ```

3. 在~/.zshrc中配置PATH和DYLD_LIBRARY_PATH。

   ```shell
   $ vi ~/.zshrc
   export PATH=$PATH:/Users/yashandb/yashandb_client/bin
   export DYLD_LIBRARY_PATH=$DYLD_LIBRARY_PATH:/Users/yashandb/yashandb_client/lib
   ```

4. 生效环境变量。

   ```shell
   $ source ~/.zshrc
   ```

## 后续操作 

- 字符集配置：macOS客户端的字符集默认为UTF8，如需调整请查阅[修改客户端字符集](../../../数据库管理/实例管理/数据库字符集配置.md#Client)。

- 可信信道：数据库客户端<->服务端可信信道默认关闭，如需进行加密通信请查阅[可信信道](../../../产品安全/加密支持/可信信道/00可信信道.md)。
