The Distributed Cluster's user operation interface layer is almost identical to that of the YAC. This product documentation will not describe it separately in each manual, but will provide a general operational guide in this document, as detailed in the description below.

## Operation and Maintenance Management

The operation and maintenance management of the Distributed Cluster includes the following aspects:

- Compute Layer Management Operations

  The Distributed Cluster includes database instances and YCS instances on each CN. For all management operations of the database instances, please refer to the [Database Management Manual](../数据库管理/基本数据库管理/实例启停) sections that adapt to the YAC.

  For the management of YCS instances, please refer to the [Cluster Service Management](../共享集群/集群服务管理/00集群服务管理) section in the YAC manual.

- Storage Layer Management Operations

  Management of the storage layer includes management operations on CNs and directly connected DN management operations.

  - Use the yfscmd command on CNs to manage all DNs in the storage network. For specific operations, please refer to the [YFS](../共享集群/集群文件系统/00集群文件系统) section in the YAC manual.
  - Use the yfscmd command on DN to manage the local file system. For specific operations, see [Managing YFS on DN](./DN上管理集群文件系统).

## Application Development

The Distributed Cluster provides SQL interfaces, PL interfaces, and various driver interfaces consistent with the YAC. For specific operational guidance, please refer to the [Development Manual](../开发手册/SQL参考手册/基本SQL元素/字面量) sections that adapt to the YAC.