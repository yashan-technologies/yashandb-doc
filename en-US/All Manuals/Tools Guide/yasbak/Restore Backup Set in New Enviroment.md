In ISC Distributed Cluster Deployment, YashanDB supports constructing a new database cluster B using the backup set of an existing database cluster A.

## Version Requirements

The versions of clusters A and B must be consistent and at least v23.2.4.100.

## Prerequisites

First, refer to the [Pre-Installation Preparation](../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/00Pre-Installation Preparation) to prepare the server and related environment for database cluster B.

## Steps

1. In database cluster A, use *yasbak* to generate a backup with the tag full_01. For detailed operations, refer to the [yasbak operation examples](Operation Examples for yasbak).

2. Compress the backup set files into a single compressed package. The format requirements for the compressed package are as follows:

   ```shell
   yashandb.tar.gz     # The file name is not constrained, but the file extension must be .tar.gz
       mn-1-1.tar.gz   # The naming convention for the compressed package is "node type-node id.tar.gz"
           full_01/... # If it is an incremental backup, compress all backup sets into one package
       cn-2-1.tar.gz
           full_01/...
       dn-3-1.tar.gz
           full_01/...
   ```

3. Execute the following command to obtain the node deployment information file nodeinfo.txt for cluster A in the current directory.

   ```shell
   $ yasbak run -c yashandb -s "list backup tag 'full_01' node info to 'nodeinfo.txt'"
   ```

4. Log in as the installation user on server 1 prepared for database cluster B, and execute the following command to generate the configuration file for database cluster B.

   > **Note**: 
   >
   > - Specify --node-info as the path to the file generated in step 3.
   > - If --data-path is not specified, the node's datapath will remain consistent with the datapath in nodeinfo.txt.
   > - Clusters A and B do not need to have the same name.
   > - There is no need to specify --mn, --cn, or --dn; the number of nodes will be automatically generated through --node-info.

   ```shell
   $ yasboot package de gen --cluster yashandb -u yashan -p password --ip 192.168.1.2,192.168.1.3,192.168.1.4 --port 22 --install-path /data/yashan/yasdb_home --node-info nodeinfo.txt
   ```

   The generated yashandb.toml file includes the following additional parameters compared to [ISC Distributed Cluster Configuration File](../yasboot/Configuration Files/ISC Distributed Cluster Deployment Configuration File). Please do not modify groupid and nodeid manually, as this may lead to the inability to restore the database.

   ```toml
   cluster = "yashandb"
   empty_cluster = true # When empty_cluster is true, it indicates that an empty cluster is to be created.
   
   [[group]]
     groupid = 1
     
     [[group.node]]
       nodeid = "1-1:1"
   ```

5. Execute the following command to install the database.

   ```shell
   $ yasboot package install -t hosts.toml
   ```

6. Execute the following command to deploy database cluster B.

   ```shell
   $ yasboot cluster deploy -t yashandb.toml -p sys_password
   ```

7. Copy the catalog directory from cluster A to the corresponding directory on server 1 of cluster B.

8. Initialize *yasbak* for cluster B.

   > **Note**:
   >
   > The -D parameter must specify the actual directory where the catalog is stored on server 1 of cluster B.

   ```shell
   $ yasbak deploy -c yashandb -a 192.168.1.3:1675 -k 96ed7a2c90e81a9e -D ./catalog -u sys -p sys_password --force
   ```

9. Copy the backup set compressed package from cluster A to the corresponding directory on server 1 of cluster B.

10. Execute the following command to distribute the backup set compressed package to all servers in cluster B.

   ```shell
   $ yasbak distribute -c yashandb -b yashandb.tar.gz
   ```

11. Execute the following command to reset cluster B.

    ```shell
    $ yasbak reset -c yashandb -p sys_password
    ```

12. Execute the following command to restore the backup set to cluster B.

    ```shell
    $ yasbak run -c yashandb -s "restore cluster from tag 'full_01'"
    ```
