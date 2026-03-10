This document introduces the installation and uninstallation procedures for the YashanDB client (Linux).

## Install YashanDB Client

The installation methods for the YashanDB client on Linux systems are divided as follows:

- [Script Installation](#ScriptInstall): Suitable for all Linux platforms, corresponding uninstallation method is [Uninstalling YashanDB Client Installed via Script](#ScriptUninstall).

- [RPM Installation](#RPMInstall): Only suitable for CentOS Linux platforms, corresponding uninstallation method is [Uninstalling YashanDB Client Installed via RPM](#RPMUninstall).

<span id="ScriptInstall" name="ScriptInstall"  class="yaslink"></span>

### Script Installation

1. Obtain the corresponding software package according to the [YashanDB Package List](../Pre-Installation Preparation/Downloading Software Packages).

2. Upload `yashandb-client-{version}-linux-x86_64.tar.gz` to a local path (taking `/home/yasdb/yashandb_client` as an example) and extract it.

   ```shell
   $ cd /home/yasdb/yashandb_client
   
   $ tar -zxf yashandb-client-{version}-linux-x86_64.tar.gz
   ```

   Check the contents after extraction.

   ```shell
   $ ll
   drwxrwxr-x. 2 yasdb yasdb      19 Feb  8 00:01 bin
   drwxrwxr-x. 2 yasdb yasdb      21 Feb  8 00:01 include
   drwxrwxr-x. 2 yasdb yasdb    4096 Feb  8 00:01 lib
   ```

3. Configure `PATH` and `LD_LIBRARY_PATH` in `~/.bashrc`.

   ```shell
   $ vi ~/.bashrc
   export PATH=$PATH:/home/yasdb/yashandb_client/bin
   export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/yasdb/yashandb_client/lib
   ```

4. Activate the environment variables.

   ```shell
   $ source ~/.bashrc
   ```

<span id="RPMInstall" name="RPMInstall"  class="yaslink"></span>

### RPM Installation

The RPM installation method is only suitable for CentOS Linux platforms and must be executed with root user privileges.

1. Obtain the corresponding software package according to the [YashanDB Package List](../Pre-Installation Preparation/Downloading Software Packages), taking `yashandb-client-{version}-1.el7.x86_64.rpm` as an example.

2. Log in to the server as the root user and upload the software package to the server (taking `/usr/local/install` as an example).

3. Run RPM.

   ```shell
   # cd /usr/local/install
   
   # rpm -ivh yashandb-client-{version}-1.el7.x86_64.rpm
   ```

4. RPM defaults to installing the client program in the `/usr/local/yashandb_client` directory. Check the contents of that directory.

   ```shell
   # cd /usr/local/yashandb_client
   
   # ll
   drwxrwxr-x. 2 root root      19 Feb 10 02:49 bin
   drwxrwxr-x. 2 root root      21 Feb 10 02:49 include
   drwxrwxr-x. 2 root root    4096 Feb 10 02:49 lib
   ```

5. Switch to the user that needs to use the YashanDB client.

6. Configure `PATH` and `LD_LIBRARY_PATH` in `~/.bashrc`.

   ```shell
   $ vi ~/.bashrc
   export PATH=$PATH:/usr/local/yashandb_client/bin
   export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/yashandb_client/lib
   ```

7. Activate the environment variables.

   ```shell
   # source ~/.bashrc
   ```

## Uninstall YashanDB Client

<span id="ScriptUninstall" name="ScriptUninstall"  class="yaslink"></span>

### Uninstalling YashanDB Client Installed via Script

1. Remove the YashanDB client-related environment variables from `~/.bashrc`.

2. Delete the client directory, such as `/home/yasdb/yashandb_client`.

<span id="RPMUninstall" name="RPMUninstall"  class="yaslink"></span>

### Uninstalling YashanDB Client Installed via RPM

1. Under the user using the YashanDB client, delete the YashanDB client-related environment variables from `~/.bashrc`.

2. Uninstall the RPM package as the root user.

   ```shell
   # rpm -q yashandb
   yashandb-client-{version}-1.el7.x86_64
   # rpm -e yashandb-client-{version}-1.el7.x86_64
   ```

3. Delete the `/usr/local/yashandb_client` directory.