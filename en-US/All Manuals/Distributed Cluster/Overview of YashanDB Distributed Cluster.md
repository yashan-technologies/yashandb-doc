When installing the YashanDB product, selecting the Distributed Deployment (SSC) form will set up a single database multi-instance cluster for the user, including a compute cluster and an intelligent distributed storage network.

In Distributed Deployment (SSC), the storage network depends on the NVMe SSD and the RDMA network card, through which the high-speed communication between computing-layer and storage-layer is archived adopting a universal NVMe-oF protocol.

Each CN (Compute Node) runs on different servers in the compute cluster, with each server connected to each server in the storage network via fiber or network.

One set of persistent file for a distributed cluster (SSC) database is stored on multiple NVMe devices through the intelligent distributed storage services. It can be read and written by all instances. All instances form a peer-to-peer cluster, and any instance can read and write all data.

## Overview of Functionality Modules

The Distributed Cluster is divided into two categories of functionality modules:

- Compute Layer Functionality Module

  The compute layer consists of a group of Compute Nodes (CN) forming a multi-instance active-active cluster. Its functional components are the same as those in the instance layer of the YAC Deployment. For a detailed description, please refer to [YAC Functionality Module Description](../共享集群/YashanDB共享集群概述.html#YAC-Functionality-Overview).

- Storage Layer Functionality Module

  The storage layer consists of a group of Data Nodes (DN) forming a distributed intelligent storage service cluster. Each DN is mounted with two NVMe devices as the system disk and data disk. Each NVMe device provides standard NVMe-oF services for the compute nodes to access. YashanDB provides the *yasfs* disk management service for users to manage the file system.

  The *yfscmd* tool on the CN allows for management of storage devices in the DN cluster. It can also directly connect to the DN to query information about storage devices on the current node using the *yfscmd* tool.

  

  