## Environment Requirements

The YashanDB client supports macOS on Apple Silicon architecture.

## Install YashanDB Client

Installing YashanDB Client on macOS is done via script installation. The corresponding uninstall method is [Uninstall YashanDB Client installed via script](../../Uninstallation/Uninstalling YashanDB Client.md#ScriptUninstall).

1. From the [YashanDB Official Website Download Center](https://download.yashandb.com/download), or contact our technical support to obtain the corresponding software package.

2. Upload `yashandb-client-{version_number}-darwin-arm64.tar.gz` to a local path (taking `/Users/yashandb/yashandb_client` as an example) and extract it.

   ```shell
   $ cd /Users/yashandb/yashandb_client
   
   $ tar -zxf yashandb-client-{version_number}-darwin-arm64.tar.gz
   ```

   Check the contents after extraction.

   ```shell
   $ ll
   drwxrwxr-x. 2 yasdb yasdb      19 Feb  8 00:01 bin
   drwxrwxr-x. 2 yasdb yasdb      21 Feb  8 00:01 include
   drwxrwxr-x. 2 yasdb yasdb    4096 Feb  8 00:01 lib
   ```

3. Configure `PATH` and `DYLD_LIBRARY_PATH` in `~/.zshrc`.

   ```shell
   $ vi ~/.zshrc
   export PATH=$PATH:/Users/yashandb/yashandb_client/bin
   export DYLD_LIBRARY_PATH=$DYLD_LIBRARY_PATH:/Users/yashandb/yashandb_client/lib
   ```

4. Activate the environment variables.

   ```shell
   $ source ~/.zshrc
   ```

##  Subsequent Operations

- Character Set Configuration: The default character set for macOS client is UTF8. To adjust this, please refer to [Modifying Client Character Set](../../../Database Administration/Instance Management/Database Character Set Configuration.md#Client).

- Trusted Channel: Database client <-> server trusted channel is disabled by default. If encrypted communication is required, please refer to [Trusted Channel](../../../Product Security/Encryption/Trusted Channel/00Trusted Channel).