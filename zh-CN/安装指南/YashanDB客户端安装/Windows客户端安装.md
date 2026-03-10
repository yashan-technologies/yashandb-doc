## 安装YashanDB客户端

1. 从[YashanDB官网下载中心](https://download.yashandb.com/download)或联系我们的技术支持获取对应的软件包。

   ```cmd
   > d:

   D:\> cd D:\yashandb-client
   D:\yashandb-client> dir
   2023/02/10  14:57    <DIR>          .
   2023/02/10  14:57    <DIR>          ..
   2023/02/10  14:55    <DIR>          bin
   2023/02/10  14:55    <DIR>          include
   2023/02/10  14:55    <DIR>          lib
   ```

2. 在Windows配置用户环境变量的工具中，为Path变量增加如下内容：

   ```cmd
   D:\yashandb-client\bin
   D:\yashandb-client\lib
   ```



## 后续操作 

- 字符集配置：Windows客户端的字符集默认为GBK，如需调整请查阅[修改客户端字符集](../../全部手册/数据库管理/实例管理/数据库字符集配置.html#Client)。

- 可信信道：数据库客户端\<->服务端可信信道默认关闭，如需进行加密通信请查阅[可信信道](../../全部手册/产品安全/加密支持/可信信道/00可信信道)。

