This article describes the installation and uninstallation steps for the YashanDB client (Windows).

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

## Uninstall YashanDB Client

1. In the Windows user environment variable configuration tool, delete the environment variables related to yashandb-client.

2. Delete the client directory, for example, D:\yashandb-client.