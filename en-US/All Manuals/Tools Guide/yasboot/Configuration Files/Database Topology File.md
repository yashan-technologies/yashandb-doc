The database topology file lists information related to the YashanDB server database and logical topology information.

***Example***: foryashandb_topology.toml

## ISC Distributed Cluster Deployment

```toml
# The following content is subject to the actual display results
 [[group]]
  group_type = "mn"  # Node group type
  group_id = 1  # Node group ID
  group_name = "mng1"  # Node group name

  [[group.node]]
    nodeid = "1-1:1"  # Node ID
    hostid = "host0001"  # Identifier of the server where the node is located
    data_path = "/data/yashandb/yashandb/data/yashandb/mn-1-1"  # DATA directory
    home_path = ""  # YASDB_HOME directory; if the path is the same as the *yasboot* database installation path, it does not need to be filled in.

[[group]]
  group_type = "cn"
  group_id = 2
  group_name = "cng1"

  [[group.node]]
    nodeid = "2-1:2"
    hostid = "host0001"
    data_path = "/data/yashandb/yashandb/data/yashandb/cn-2-1"
    home_path = ""

[[group]]
  group_type = "dn"
  group_id = 3
  group_name = "dng1"

  [[group.node]]
    nodeid = "3-1:3"
    hostid = "host0001"
    data_path = "/data/yashandb/yashandb/data/yashandb/dn-3-1"
    home_path = ""

[[group]]
  group_type = "dn"
  group_id = 4
  group_name = "dng2"

  [[group.node]]
    nodeid = "4-1:4"
    hostid = "host0001"
    data_path = "/data/yashandb/yashandb/data/yashandb/dn-4-1"
    home_path = ""

[[group]]
  group_type = "dn"
  group_id = 5
  group_name = "dng3"

  [[group.node]]
    nodeid = "5-1:5"
    hostid = "host0001"
    data_path = "/data/yashandb/yashandb/data/yashandb/dn-5-1"
    home_path = ""
```

## Standalone Deployment

***Example***: foryashandb_topology.toml

```toml
[[group]]
  group_type = "db"
  group_id = 1
  group_name = "dbg1"

  [[group.node]]
    nodeid = "1-1:1"
    hostid = "host0001"
    data_path = "/data/yashandb/yashandb/data/yashandb/db-1-1"
    home_path = ""

  [[group.node]]
    nodeid = "1-2:2"
    hostid = "host0001"
    data_path = "/data/yashandb/yashandb/data/yashandb/db-1-2"
    home_path = ""
```

## YAC/Distributed Cluster Deployment

***Example***: foryashandb_topology.toml

```toml
[[group]]
[[group]]
  group_type = "ce"
  group_id = 1
  group_name = "ceg1"

  [[group.node]]
    nodeid = "1-2:2"
    hostid = "host0001"
    data_path = "/data/cluster/ce-1-2"
    home_path = ""

  [[group.node]]
    nodeid = "1-1:1"
    hostid = "host0001"
    data_path = "/data/cluster/ce-1-1"
    home_path = ""
```
