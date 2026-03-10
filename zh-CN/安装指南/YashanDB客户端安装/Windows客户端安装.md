## 安装YashanDB客户端

1. 根据[YashanDB软件包清单](../安装前准备/下载软件包)获取对应的软件包，并其解压至本地路径。

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

- 字符集配置：Windows客户端的字符集默认为GBK，如需调整请查阅[修改客户端字符集](../../全部手册/数据库管理/基本数据库管理/字符集配置.html#Client)。

- 可信信道：数据库客户端\<->服务端可信信道默认关闭，如需进行加密通信请查阅[可信信道](../../全部手册/数据库管理/基本数据库管理/SSL可信信道配置/数据库服务端SSL可信信道)。

