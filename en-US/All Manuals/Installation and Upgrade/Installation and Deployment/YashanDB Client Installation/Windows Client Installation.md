## Install YashanDB Client

1. Obtain the corresponding software package according to the [YashanDB Software Package List](../Pre-Installation Preparation/Downloading Software Packages) and extract it to a local path.

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

2. In the Windows user environment variable configuration tool, add the following content to the Path variable:

   ```cmd
   D:\yashandb-client\bin
   D:\yashandb-client\lib
   ```



##  Subsequent Operations

- Character Set Configuration: The default character set for Windows clients is GBK. To adjust this, please refer to [Modifying Client Character Set](../../../数据库管理/基本数据库管理/字符集配置.html#Client).

- Trusted Channel: Database client \<-> server trusted channel is disabled by default. If encrypted communication is required, please refer to [Trusted Channel](../../../数据库管理/基本数据库管理/SSL可信信道配置/数据库服务端SSL可信信道).

