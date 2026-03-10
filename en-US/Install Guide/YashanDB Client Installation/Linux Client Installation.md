## Install YashanDB Client

The installation methods for the YashanDB client on Linux systems are divided as follows:

- [Script Installation](#ScriptInstall): Suitable for all Linux platforms, corresponding uninstallation method is [Uninstalling YashanDB Client Installed via Script](../../All Manuals/Installation and Upgrade/Uninstallation/Uninstalling YashanDB Client.html#ScriptUninstall).

- [RPM Installation](#RPMInstall): Only suitable for CentOS Linux platforms, corresponding uninstallation method is [Uninstalling YashanDB Client Installed via RPM](../../All Manuals/Installation and Upgrade/Uninstallation/Uninstalling YashanDB Client.html#RPMUninstall).

<span id="ScriptInstall" name="ScriptInstall"  class="yaslink"></span>

### Script Installation

1. From the [YashanDB Official Website Download Center](https://download.yashandb.com/download), or contact our technical support to obtain the corresponding software package.

2. Upload `yashandb-client-{version_number}-linux-x86_64.tar.gz` to a local path (taking `/home/yasdb/yashandb_client` as an example) and extract it.

   ```shell
   $ cd /home/yasdb/yashandb_client
   
   $ tar -zxf yashandb-client-{version_number}-linux-x86_64.tar.gz
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

1. From the [YashanDB Official Website Download Center](https://download.yashandb.com/download), or contact our technical support to obtain the corresponding software package. Taking `yashandb-client-{version_number}-1.el7.x86_64.rpm` as an example.

2. Log in to the server as the root user and upload the software package to the server (taking `/usr/local/install` as an example).

3. Run RPM.

   ```shell
   # cd /usr/local/install
   
   # rpm -ivh yashandb-client-{version_number}-1.el7.x86_64.rpm
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



##  Subsequent Operations

- Character Set Configuration: The default character set for Linux client is UTF8. To adjust this, please refer to [Modifying Client Character Set](../../All Manuals/Database Administration/Instance Management/Database Character Set Configuration.html#Client).

- Trusted Channel: Database client \<-> server trusted channel is disabled by default. If encrypted communication is required, please refer to [Trusted Channel](../../All Manuals/Product Security/Encryption/Trusted Channel/00Trusted Channel).

