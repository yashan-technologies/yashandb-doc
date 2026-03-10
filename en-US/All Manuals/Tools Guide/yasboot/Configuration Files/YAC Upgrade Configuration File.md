YAC upgrade configuration file lists the disk information that needs to be configured when upgrading YAC from version 23.2.x.x and earlier to version 23.4.x.x and above.

***Example***: fordisk_config.toml

```toml
[[group]]
    group_id = 1                    # YAC group id
    disk_found_path = "/dev/yfs"    # Specify the disk discovery path, which is the parent directory of the data disks. Disk information can be queried using the command yfscmd show disk.
    system_data = ["/dev/yfs/sys0", "/dev/yfs/sys1", "/dev/yfs/sys2"]   # Specify the planned system disk paths.

[[group]]
    group_id = 2
    disk_found_path = "/dev/yfs2"
    system_data = ["/dev/yfs2/sys0", "/dev/yfs2/sys1", "/dev/yfs2/sys2"]
```
