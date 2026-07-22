## Uninstall YashanDB Client (Windows)

1. In the Windows user environment variable configuration tool, delete the environment variables related to yashandb-client.

2. Delete the client directory, for example, D:\yashandb-client.

## Uninstall YashanDB Client (Linux)

<span id="ScriptUninstall" name="ScriptUninstall" ></span>

### Uninstalling YashanDB Client Installed via Script

1. Remove the YashanDB client-related environment variables from `~/.bashrc`.

2. Delete the client directory, such as `/home/yasdb/yashandb_client`.

<span id="RPMUninstall" name="RPMUninstall" ></span>

### Uninstalling YashanDB Client Installed via RPM

1. Under the user using the YashanDB client, delete the YashanDB client-related environment variables from `~/.bashrc`.

2. Uninstall the RPM package as the root user.

   ```shell
   # rpm -q yashandb
   yashandb-client-{version_number}-1.el7.x86_64
   # rpm -e yashandb-client-{version_number}-1.el7.x86_64
   ```

3. Delete the `/usr/local/yashandb_client` directory.