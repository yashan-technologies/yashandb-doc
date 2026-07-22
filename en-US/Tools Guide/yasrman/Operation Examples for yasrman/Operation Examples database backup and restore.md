This example demonstrates backup and recovery operations using the sys user, while table creation and query operations are performed by a regular user. Users, passwords, IP addresses, ports, paths, and other items listed in the text should be replaced with actual values.

In both Standalone/YAC/Distributed Cluster Deployment, the *yasrman* command and its usage are exactly the same, whereas there are slight differences in ISC Distributed Cluster Deployment.

## Standalone/YAC/Distributed Cluster Deployment

1. Create a catalog file.

    ```shell
    $ yasrman sys/********@192.168.1.2:1688 -c "create catalog" -D /home/yashan/catalog

    create catalog successfully
    ```

2. List the existing default configuration items.

    ```shell
    $ yasrman sys/********@192.168.1.2:1688 -c "show all" -D /home/yashan/catalog
    +---------------------------+-----------+----------------+
    |           NAME            |  DEFAULT  |      VALUE     |
    +---------------------------+-----------+----------------+
    | PARALLELISM               | 2         | 2              |
    | SECTION SIZE              | 134217728 | 134217728      |
    | COMPRESSION ALGORITHM     | NONE      | NONE           |
    | COMPRESSION LEVEL         | LOW       | LOW            |
    | DEST                      | SERVER    | SERVER         |
    +---------------------------+-----------+----------------+
    ```

3. Configure backup functionality.

    ```shell
    $ yasrman sys/********@192.168.1.2:1688 -c "configure PARALLELISM 4" -D /home/yashan/catalog

    configure successfully

    $ yasrman sys/********@192.168.1.2:1688 -c "configure COMPRESSION ALGORITHM ZSTD" -D /home/yashan/catalog

    configure successfully

    $ yasrman sys/********@192.168.1.2:1688 -c "configure COMPRESSION LEVEL HIGH" -D /home/yashan/catalog

    configure successfully
    ```

    After configuration, use show all to check the configured items.

    ```shell
    $ yasrman sys/********@192.168.1.2:1688 -c "show all" -D /home/yashan/catalog
    +---------------------------+-----------+----------------+
    |           NAME            |  DEFAULT  |      VALUE     |
    +---------------------------+-----------+----------------+
    | PARALLELISM               | 2         | 4              |
    | SECTION SIZE              | 134217728 | 134217728      |
    | COMPRESSION ALGORITHM     | NONE      | ZSTD           |
    | COMPRESSION LEVEL         | LOW       | HIGH           |
    | DEST                      | SERVER    | SERVER         |
    +---------------------------+-----------+----------------+
    ```

    Use the following clear command to reset the compression algorithm configuration:

    ```shell
    $ yasrman sys/********@192.168.1.2:1688 -c "configure COMPRESSION ALGORITHM CLEAR" -D /home/yashan/catalog

    configure successfully

    $ yasrman sys/********@192.168.1.2:1688 -c "show all" -D /home/yashan/catalog
    +---------------------------+-----------+----------------+
    |           NAME            |  DEFAULT  |      VALUE     |
    +---------------------------+-----------+----------------+
    | PARALLELISM               | 2         | 4              |
    | SECTION SIZE              | 134217728 | 134217728      |
    | COMPRESSION ALGORITHM     | NONE      | NONE           |
    | COMPRESSION LEVEL         | LOW       | HIGH           |
    | DEST                      | SERVER    | SERVER         |
    +---------------------------+-----------+----------------+
    ```

4. Simulate business data generation.

    ```sql
    -- Log in as a regular user to the *yasql* client and execute the following:
    CREATE TABLE age(a int);
    INSERT INTO age values(1);
    COMMIT;
    exit
    ```

5. Perform a full backup.

    ```shell
    $ yasrman sys/********@192.168.1.2:1688 -c "backup database tag 'full_1' format 'full_bak_1'" -D /home/yashan/catalog

    backup successfully
    ```

6. Perform an incremental backup.

    ```shell
    # Perform level 0 baseline backup
    $ yasrman sys/********@192.168.1.2:1688 -c "backup database incremental level 0 tag 'incr_1' format 'incr_bak_1'" -D /home/yashan/catalog

    backup successfully

    # Perform level 1 incremental backup
    $ yasrman sys/********@192.168.1.2:1688 -c "backup database incremental level 1 tag 'incr_2' format 'incr_bak_2'" -D /home/yashan/catalog

    backup successfully
    ```

7. View the backup set.

    ```shell
    $ yasrman sys/********@192.168.1.2:1688 -c "list backup" -D /home/yashan/catalog
    Group: tag: full_1, format: /home/yashan/yasdata/backup/full_1, connect url: 192.168.1.2:1688, nodeCount: 1, distribution: FALSE, isClient: FALSE, offset: 0
        backup path: /home/yashan/yasdata/backup/full_1 

    Group: tag: incr_1, format: /home/yashan/yasdata/backup/incr_1, connect url: 192.168.1.2:1688, nodeCount: 1, distribution: FALSE, isClient: FALSE, offset: 12288
        backup path: /home/yashan/yasdata/backup/incr_1 

    Group: tag: incr_2, format: /home/yashan/yasdata/backup/incr_2, connect url: 192.168.1.2:1688, nodeCount: 1, distribution: FALSE, isClient: FALSE, offset: 24576
        backup path: /home/yashan/yasdata/backup/incr_2 
    ```

8. Restore the backup set.

    ```shell
    # Preliminary restore preparation, clean environment
    $ yasboot cluster clean -c yashandb --restore --with-arch

    # Execute restore
    $ yasrman sys/********@192.168.1.2:1688 -c "restore database from tag 'incr_2'" -D /home/yashan/catalog 

    restore successfully
    ```

9. Check if data was restored successfully.

    ```sql
    select a from age;

            A 
    ------------ 
            1

    1 row fetched.
    ```

## ISC Distributed Cluster Deployment

1. Create a catalog file.

    ```shell
    $ yasrman sys/********@192.168.1.3:1688 -c "create catalog" -D /home/yashan/catalog
    ```

2. List the existing default configuration items.

    ```shell
    $ yasrman sys/********@192.168.1.3:1688 -c "show all" -D /home/yashan/catalog
    +---------------------------+-----------+----------------+
    |           NAME            |  DEFAULT  |      VALUE     |
    +---------------------------+-----------+----------------+
    | PARALLELISM               | 2         | 2              |
    | SECTION SIZE              | 134217728 | 134217728      |
    | COMPRESSION ALGORITHM     | NONE      | NONE           |
    | COMPRESSION LEVEL         | LOW       | LOW            |
    | DEST                      | SERVER    | SERVER         |
    | DSTB NODES                | ''        | ''             |
    +---------------------------+-----------+----------------+
    ```

3. Configure backup functionality.

    ```shell
    $ yasrman sys/********@192.168.1.3:1688 -c "configure PARALLELISM 4" -D /home/yashan/catalog

    configure successfully

    $ yasrman sys/********@192.168.1.3:1688 -c "configure COMPRESSION ALGORITHM ZSTD" -D /home/yashan/catalog

    configure successfully

    $ yasrman sys/********@192.168.1.3:1688 -c "configure COMPRESSION LEVEL HIGH" -D /home/yashan/catalog

    configure successfully

    $ yasrman sys/********@192.168.1.2:1688 -c 'configure dstb_nodes '/home/yashan/catalog/dstb_nodes_file'' -D /home/yashan/catalog

    configure successfully
    ```

    After configuration, use show all to check the configured items.

    ```shell
    $ yasrman sys/********@192.168.1.3:1688 -c "show all" -D /home/yashan/catalog
    +---------------------------+-----------+----------------+
    |           NAME            |  DEFAULT  |      VALUE     |
    +---------------------------+-----------+----------------+
    | PARALLELISM               | 2         | 4              |
    | SECTION SIZE              | 134217728 | 134217728      |
    | COMPRESSION ALGORITHM     | NONE      | ZSTD           |
    | COMPRESSION LEVEL         | LOW       | HIGH           |
    | DEST                      | SERVER    | SERVER         |
    | DSTB NODES                | ''        | /home/yashan/catalog/dstb_nodes_file |
    +---------------------------+-----------+----------------+
    ```

    Use the following clear command to reset the compression algorithm configuration:

    ```shell
    $ yasrman sys/********@192.168.1.3:1688 -c "configure COMPRESSION ALGORITHM CLEAR" -D /home/yashan/catalog

    configure successfully

    $ yasrman sys/password@192.168.1.3:1688 -c "show all" -D /home/yashan/catalog
    +---------------------------+-----------+----------------+
    |           NAME            |  DEFAULT  |      VALUE     |
    +---------------------------+-----------+----------------+
    | PARALLELISM               | 2         | 4              |
    | SECTION SIZE              | 134217728 | 134217728      |
    | COMPRESSION ALGORITHM     | NONE      | NONE           |
    | COMPRESSION LEVEL         | LOW       | HIGH           |
    | DEST                      | SERVER    | SERVER         |
    | DSTB NODES                | ''        | ''             |
    +---------------------------+-----------+----------------+
    ```

4. Simulate business data generation.

    ```sql
    -- Log in as a regular user to the *yasql* client and execute the following:
    CREATE TABLE age(a int);
    INSERT INTO age values(1);
    COMMIT;
    exit
    ```

5. Perform a full backup.

    ```shell
    $ yasrman sys/********@192.168.1.3:1688 -c "backup cluster tag 'full_1' format 'full_bak_1'" -D /home/yashan/catalog
    Node 0: MN, 1-1    192.168.1.2:1678    role PRIMARY
    Node 1: MN, 1-2    192.168.1.3:1678    role STANDBY
    Node 2: MN, 1-3    192.168.1.4:1678    role STANDBY
    Node 3: CN, 2-1    192.168.1.3:1688    role PRIMARY
    Node 4: CN, 2-2    192.168.1.4:1688    role PRIMARY
    Node 5: DN, 3-1    192.168.1.4:1698    role PRIMARY
    Node 6: DN, 3-2    192.168.1.2:1698    role STANDBY
    Node 7: DN, 3-3    192.168.1.3:1698    role STANDBY
    backup database node 1-1
    backup database node 2-1
    backup database node 2-2
    backup database node 3-1
    backup cluster successfully
    ```

6. Perform an incremental backup.

    ```shell
    # Perform level 0 baseline backup
    $ yasrman sys/********@192.168.1.3:1688 -c "backup cluster incremental level 0 tag 'incr_1' format 'incr_bak_1'" -D /home/yashan/catalog
    Node 0: MN, 1-1    192.168.1.2:1678    role PRIMARY
    Node 1: MN, 1-2    192.168.1.3:1678    role STANDBY
    Node 2: MN, 1-3    192.168.1.4:1678    role STANDBY
    Node 3: CN, 2-1    192.168.1.3:1688    role PRIMARY
    Node 4: CN, 2-2    192.168.1.4:1688    role PRIMARY
    Node 5: DN, 3-1    192.168.1.4:1698    role PRIMARY
    Node 6: DN, 3-2    192.168.1.2:1698    role STANDBY
    Node 7: DN, 3-3    192.168.1.3:1698    role STANDBY
    backup database node 1-1
    backup database node 2-1
    backup database node 2-2
    backup database node 3-1
    backup cluster successfully

    # Perform level 1 incremental backup
    $ yasrman sys/********@192.168.1.3:1688 -c "backup cluster incremental level 1 tag 'incr_2' format 'incr_bak_2'" -D /home/yashan/catalog
    Node 0: MN, 1-1    192.168.1.2:1678    role PRIMARY
    Node 1: MN, 1-2    192.168.1.3:1678    role STANDBY
    Node 2: MN, 1-3    192.168.1.4:1678    role STANDBY
    Node 3: CN, 2-1    192.168.1.3:1688    role PRIMARY
    Node 4: CN, 2-2    192.168.1.4:1688    role PRIMARY
    Node 5: DN, 3-1    192.168.1.4:1698    role PRIMARY
    Node 6: DN, 3-2    192.168.1.2:1698    role STANDBY
    Node 7: DN, 3-3    192.168.1.3:1698    role STANDBY
    backup database node 1-1
    backup database node 2-1
    backup database node 2-2
    backup database node 3-1
    backup cluster successfully
    ```

7. View the backup set.

    ```shell
    $ yasrman sys/********@192.168.1.3:1688 -c "list backup" -D /home/yashan/catalog
    Group: tag: full_1, format full_bak_1, connect url: 192.168.1.3:1688, nodeCount: 8, distribution: TRUE, offset: 86016
      backupset ID: 0
        node 1-1, type MN, url 192.168.1.2:1678, role PRIMARY
        backup path: /home/yashan/mn/mn-1-1/backup/full_bak_1 
      backupset ID: 1
        node 1-2, type MN, url 192.168.1.3:1678 , role STANDBY
        backup path:  
      backupset ID: 2
        node 1-3, type MN, url 192.168.1.4:1678, role STANDBY
        backup path: 
      backupset ID: 3
        node 2-1, type CN, url 192.168.1.3:1688, role PRIMARY
        backup path: /home/yashan/cn/cn-2-1/backup/full_bak_1 
      backupset ID: 4
        node 2-2, type CN, url 192.168.1.4:1688, role PRIMARY
        backup path: /home/yashan/cn/cn-2-1/backup/full_bak_1 
      backupset ID: 5
        node 3-1, type DN, url 192.168.1.4:1698, role PRIMARY
        backup path: /home/yashan/dn/dn-3-1/backup/full_bak_1 
      backupset ID: 6
        node 3-2, type DN, url 192.168.1.2:1698, role STANDBY
        backup path:  
      backupset ID: 7
        node 3-3, type DN, url 192.168.1.3:1698, role STANDBY
        backup path:  

    Group: tag: incr_1, format incr_bak_1, connect url: 127.0.0.1:1688, nodeCount: 8, distribution: TRUE, offset: 172032
      backupset ID: 0
        node 1-1, type MN, url 192.168.1.2:1678, role PRIMARY
        backup path: /home/yashan/mn/mn-1-1/backup/full_bak_1 
      backupset ID: 1
        node 1-2, type MN, url 192.168.1.3:1678 , role STANDBY
        backup path:  
      backupset ID: 2
        node 1-3, type MN, url 192.168.1.4:1678, role STANDBY
        backup path: 
      backupset ID: 3
        node 2-1, type CN, url 192.168.1.3:1688, role PRIMARY
        backup path: /home/yashan/cn/cn-2-1/backup/full_bak_1 
      backupset ID: 4
        node 2-2, type CN, url 192.168.1.4:1688, role PRIMARY
        backup path: /home/yashan/cn/cn-2-1/backup/full_bak_1 
      backupset ID: 5
        node 3-1, type DN, url 192.168.1.4:1698, role PRIMARY
        backup path: /home/yashan/dn/dn-3-1/backup/full_bak_1 
      backupset ID: 6
        node 3-2, type DN, url 192.168.1.2:1698, role STANDBY
        backup path:  
      backupset ID: 7
        node 3-3, type DN, url 192.168.1.3:1698, role STANDBY
        backup path:  

    Group: tag: incr_2, format incr_bak_2, connect url: 192.168.1.3:1688, nodeCount: 8, distribution: TRUE, offset: 258048
      backupset ID: 0
        node 1-1, type MN, url 192.168.1.2:1678, role PRIMARY
        backup path: /home/yashan/mn/mn-1-1/backup/full_bak_1 
      backupset ID: 1
        node 1-2, type MN, url 192.168.1.3:1678 , role STANDBY
        backup path:  
      backupset ID: 2
        node 1-3, type MN, url 192.168.1.4:1678, role STANDBY
        backup path: 
      backupset ID: 3
        node 2-1, type CN, url 192.168.1.3:1688, role PRIMARY
        backup path: /home/yashan/cn/cn-2-1/backup/full_bak_1 
      backupset ID: 4
        node 2-2, type CN, url 192.168.1.4:1688, role PRIMARY
        backup path: /home/yashan/cn/cn-2-1/backup/full_bak_1 
      backupset ID: 5
        node 3-1, type DN, url 192.168.1.4:1698, role PRIMARY
        backup path: /home/yashan/dn/dn-3-1/backup/full_bak_1 
      backupset ID: 6
        node 3-2, type DN, url 192.168.1.2:1698, role STANDBY
        backup path:  
      backupset ID: 7
        node 3-3, type DN, url 192.168.1.3:1698, role STANDBY
        backup path:  
      ```

8. Restore the backup set.

    ```shell
    # Preliminary restore preparation, clean the environment and restart the database
    $ yasboot cluster clean -c yashandb --restore --force

    # Execute restore
    $ yasrman sys/********@192.168.1.3:1688 -c "restore cluster from tag 'incr_2'" -D /home/yashan/catalog 
    Node 0: MN, 1-1    192.168.1.2:1678    role PRIMARY
    Node 1: MN, 1-2    192.168.1.3:1678    role STANDBY
    Node 2: MN, 1-3    192.168.1.4:1678    role STANDBY
    Node 3: CN, 2-1    192.168.1.3:1688    role PRIMARY
    Node 4: CN, 2-2    192.168.1.4:1688    role PRIMARY
    Node 5: DN, 3-1    192.168.1.4:1698    role PRIMARY
    Node 6: DN, 3-2    192.168.1.2:1698    role STANDBY
    Node 7: DN, 3-3    192.168.1.3:1698    role STANDBY
    restore database node 1-1, URL 192.168.1.2:1678
    build database node 1-2, URL 192.168.1.3:1678
    build database node 1-3, URL 192.168.1.4:1678
    restore database node 3-1, URL 192.168.1.4:1698
    build database node 3-2, URL 192.168.1.2:1698
    build database node 3-3, URL 192.168.1.3:1698
    restore database node 2-2, URL 192.168.1.4:1688
    restore database node 2-1, URL 192.168.1.3:1688
    restore cluster successfully
    ```

9. Check if data was restored successfully.

    ```sql
    select a from age;

              A 
    ------------ 
              1

    1 row fetched.
    ```
