> **Caution**: 
> 
> A single node YashanDB (yashan mode) in Standalone Deployment can be quickly and lightly deployed through Docker containers and the specific image package.

## Preparations

1. Log in to the Docker container host machine as the installation user.

2. Create 2 directories for mapping YashanDB's data directory and *yasboot* tool directory:

    ```shell
    $ cd /home/yashan

    $ mkdir yasboot
    $ mkdir data
    ```

## Step 1: Import Installation Package

Execute the following command to import the installation package:

```shell
$ docker load -i yashandb-image-{version_number}-linux-x86_64.tar.gz
```

After the import is complete, you can use the `docker images` command to check the imported images, the echo output will be as follows:

```shell
$ docker images
REPOSITORY                                 TAG                                               IMAGE ID       CREATED        SIZE
yashandb                                   yashandb-image-{version_number}-linux-x86_64   2d27c4f55115   29 hours ago   912MB
```

## Step 2: Start Container

Execute the following command to start the container:

```shell
$ docker run -d -p 1688:1688 -v /home/yashan/data:/data/yashan -v /home/yashan/yasboot:/home/yashan/.yasboot -e SYS_PASSWD=password --name yashandb yashandb:yashandb-image-{version_number}-linux-x86_64
```

Description of container running related parameters:

|Parameter Name |Parameter Description |
| ------ | ------------------------------------------ |
| -d             | Run the container in the background                    |
| -p             | Specify container port mapping, the exposed port inside the container is 1688 |
| -v             | Specify container file path mapping, mounting YashanDB's data directory and *yasboot* tool directory prepared on the host machine into the container. For multiple directories, specify multiple times |
| --name         | Specify the container name                               |
| -e             | Specify environment variables                             |

Supported environment variables are as follows:

|Parameter Name |Parameter Description |
| -------------- | ------------------------------------------------------------ |
| SYS_PASSWD          | Password for sys user (must be configured)            |
| DB_BLOCK_SIZE       | Data block size                                        |
| YAS_{database creation parameters} | Supports all [database creation parameters](../../Tools Guide/yasboot/Database Creation Parameters), format is `YAS_parameter name` (for example, parameter REDO_FILE_SIZE should be written as YAS_REDO_FILE_SIZE), parameters configured here cannot be modified after database creation |

## Step 3: Check Installation Result

Execute the following command to check the installation result:

```shell
$ docker logs yashandb
init
host host0001 openssl version: OpenSSL 1.1.1l  24 Aug 2021
OpenSSL version is 1.1.1 or greater
host host0001 openssl version: OpenSSL 1.1.1l  24 Aug 2021
OpenSSL version is 1.1.1 or greater
+------------------------------------------------------------------------------------------------------+
| hostid   | group | node_type | node_name | listen_addr  | replication_addr | data_path               |
+------------------------------------------------------------------------------------------------------+
| host0001 | dbg1  | db        | 1-1       | 0.0.0.0:1688 | 127.0.0.1:1689   | /data/yashan/yasdb_data |
+----------+-------+-----------+-----------+--------------+------------------+-------------------------+

Generate config completed
    REDO_FILE_SIZE = "128M"
host host0001 openssl version: OpenSSL 1.1.1l  24 Aug 2021
OpenSSL version is 1.1.1 or greater
checking install profile.toml...
install version: yashandb 23.2.4.24
update host to yasom...
+------------------------------------------------------------------------------------------------------------+
| type | uuid             | name               | hostid | index    | status  | return_code | progress | cost |
+------------------------------------------------------------------------------------------------------------+
| task | cebb2746ef35401b | DeployYasdbCluster | -      | yashandb | SUCCESS | 0           | 100      | 33   |
+------+------------------+--------------------+--------+----------+---------+-------------+----------+------+
task completed, status: SUCCESS
```

When the status of the DeployYasdbCluster task is SUCCESS, it indicates that the database installation is successful.

## Step 4: Connect to Database

1. Execute the following command to enter the container:

    ```shell
    $ docker exec -it yashandb /bin/bash -l
    ```

2. Use the [yasql](../../Tools Guide/yasql/00yasql) tool to connect to the database and check the instance status.

    ```shell
    $ yasql sys/********@0.0.0.0:1688
    YashanDB SQL Enterprise Edition Release {version} aarch64

    Connected to:
    YashanDB Server Enterprise Edition Release {version} aarch64 - Linux

    SQL> SELECT STATUS FROM V$INSTANCE;

    STATUS        
    ------------- 
    OPEN         


    SQL> SELECT database_name FROM v$database;

    DATABASE_NAME                                                    
    ---------------------------------------------------------------- 
    yashandb                                                        


    ```

3. (Optional) Create a database user and grant permissions, for more operations please refer to [User Management](../../Product Security/Identity Identification and Authentication/User/00User).

   ```shell
   SQL> CREATE USER sales IDENTIFIED BY sales;
      
   SQL> GRANT CONNECT TO SALES;
   ```

## Appendix: Manage Database via Docker

### Enter Container

```shell
$ docker exec -it yashandb /bin/bash -l
```

After entering the container, you can use tools like [yasql](../../Tools Guide/yasql/00yasql), [yasboot](../../Tools Guide/yasboot/00yasboot) to connect or manage the database.

### Start and Stop Database via Container

You can directly control the database cluster using Docker commands, which is equivalent to entering the container and executing *yasboot* tool commands for starting and stopping.

- Stop Database

  ```shell
  $ docker stop yashandb
  ```

- Start Database

  ```shell
  $ docker start yashandb
  ```

- Restart Database

  ```shell
  $ docker restart yashandb
  ```
