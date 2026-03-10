本文介绍YashanDB客户端（Windows）的安装与卸载的操作步骤。

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

## 卸载YashanDB客户端

1. 在Windows配置用户环境变量的工具中，删除yashandb-client相关环境变量。

2. 删除客户端目录，例如D:\yashandb-client。
