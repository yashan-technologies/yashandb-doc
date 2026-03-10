The server expansion configuration file lists the physical information of the newly added servers for the YashanDB server.



>**Caution**:
>
> The sample content of the configuration files provided in this chapter is mainly used to introduce the composition and meaning of configuration items. To avoid introducing unnecessary errors, it is **not recommended** to directly copy the sample content to manually construct configuration files.



***Example***: forhosts_add.toml

```toml
# The following content is subject to the actual display results
cluster = "yashandb" # The name of the database being expanded
add_yasdba = true     # Whether to add the server user to the YASDBA user group

[[host]]
  hostid = "host0002"   # Identifier for the server where the database instance resides
  user = "yashan"       # SSH user to log into the server (database installation user)
  password = "password" # SSH login password
  ip = "192.168.1.2"    # Database server
  port = 22             # SSH port for logging into the server
  path = "/data/yashan/yasdb_home" # Product installation path, ensure this path is empty; changes post-expansion will not take effect unless reinstalled
  log_path = "/data/yashan/log"    # Log path for yasom and yasagent
  [host.yasagent]
    [host.yasagent.config]
      LISTEN_ADDR = "192.168.1.2:1676" # Port listened to by yasagent
```
