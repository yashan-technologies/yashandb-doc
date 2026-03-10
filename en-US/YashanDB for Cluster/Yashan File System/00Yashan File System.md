## System Overview

The Yashan Cluster File System (hereinafter referred to as YFS) is a user-space storage service component of YashanDB, providing file system and disk group management capabilities for managing shared storage.

With YFS, users can perform basic file/folder operations such as creation, deletion, and browsing on the shared storage. Additionally, YFS offers important features such as managing diskgroups, failure groups, etc., to support high availability of YAC storage.

## High Performance IO

YFS bypasses the system cache and performs direct IO on disk devices, writing to and reading from block devices synchronously, achieving performance metrics close to that of raw devices.

In the YAC Deployment mode, YFS manages the shared storage mounted on each cluster server, providing a consistent and efficient file management interface.

YashanDB uses YFS to enable concurrent read and write operations across multiple servers in YAC, fully utilizing the computing power of the cluster system.

## Compatible API

Users can manage YFS through a dedicated tool *yfscmd*, performing basic file management operations similar to general file systems, such as creating and deleting directories or files.

YFS also provides a rich set of API interfaces, allowing client programs (such as YashanDB) to execute storage management by calling these APIs, achieving component pluggability.

The YFS API is compatible with most operations of general file systems.

## High Availability

YFS manages disk devices through diskgroups, supporting high data availability through multiple failure groups and a multi-replica mechanism. It also offers configurable redundancy levels, allowing users to determine data redundancy based on business characteristics.

In addition to achieving data high availability through diskgroups, YFS also supports service high availability. When the number of cluster servers changes or individual servers exit abnormally, YFS can automatically adjust and restore service capabilities.

Furthermore, since YFS uses the same redo and checkpoint mechanisms as the DB to ensure the consistency and reliability of YFS state, the system can automatically recover from abnormal reboots when the entire cluster experiences issues.

## Service Start and Stop

Currently, the YFS service is a submodule managed by YCS, and the start and stop of YFS are included in the start and stop processes of YCS.

```shell
# Start YFS
$ ycsctl start ycs

# Stop YFS
$ ycsctl stop ycs
```
