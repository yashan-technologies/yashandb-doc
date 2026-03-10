## Install YashanDB Client

1. From the [YashanDB Official Website Download Center](https://download.yashandb.com/download), or contact our technical support to obtain the corresponding software package.

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

- Character Set Configuration: The default character set for Windows client is GBK. To adjust this, please refer to [Modifying Client Character Set](../../../Database Administration/Instance Management/Database Character Set Configuration.html#Client).

- Trusted Channel: Database client \<-> server trusted channel is disabled by default. If encrypted communication is required, please refer to [Trusted Channel](../../../Product Security/Encryption/Trusted Channel/00Trusted Channel).

