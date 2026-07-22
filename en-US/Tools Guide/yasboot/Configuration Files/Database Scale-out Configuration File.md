The cluster scale-out configuration file lists the YashanDB server database scale-out-related information and logical cluster architecture information.

Among them, the configuration information related to parameters is divided into the following two parts:

- [Database Creation Parameters](../Database Creation Parameters): These parameters are used to control relevant information when creating a database instance and are configured at the [[group]] instance group level, which means that multiple instances within each instance group create a consistent database, and the configuration is only effective for the scale-out of the instance group.
- [Configuration Parameters](../../../Reference Manual/Configuration Parameters): Configuration parameter information for each database instance, configured at the [[group.node]] instance level.



>**Caution**:
>
> The sample content of the configuration files provided in this chapter is mainly used to introduce the composition and meaning of configuration items. To avoid introducing unnecessary errors, it is **not recommended** to directly copy the sample content to manually construct configuration files.



***Example***: foryashandb_add.toml
```toml
# The following content is subject to the actual display results
cluster = "yashandb" # The name of the cluster to be expanded
mode = "YASHAN" # Syntax mode, only applicable to Standalone Deployment 
yas_type = "DE"		 # Deployment mode, cannot be modified

[[group]]
  group_type = "mn"	 # The type of the newly added instance/instance group
  name = "mng1"		 # The name of the instance group, name of the group to which the new instance belongs/new instance group name
  
  [group.config]     # Build database parameters that can be configured when adding a new instance group
    CHARACTER_SET = "utf8"
    ISARCHIVELOG = true
    REDO_FILE_NUM = "4"
    REDO_FILE_SIZE = "128M"
  
  [[group.node]]
    data_path = "/data/yashan/yasdb_data" # Path for DATA directory, modification after scale-out will not take effect unless reinstalled
    hostid = "host0002" # Identifier of the server where the database instance is located; modification after scale-out will not take effect unless reinstalled
    role = 2			# The primary/standby role of the database, effective when adding new instance group
    [group.node.config] # All database parameters can be configured, default values are used if not configured, modifications after scale-out can take effect (read-only parameters cannot be modified)
      DIN_ADDR = "192.168.3.149:1679"
      LISTEN_ADDR = "192.168.3.149:1678"
      REPLICATION_ADDR = "192.168.3.149:1680"
      RUN_LOG_LEVEL = "INFO"
      RUN_LOG_FILE_PATH = "/data/yashan/log"
      SLOW_LOG_FILE_PATH = "/data/yashan/log"
```
