Table space backup and recovery currently only supports execution in Standalone Deployment mode.

1. Create a catalog file.

    ```shell
    $ yasrman sys/********@192.168.1.2:1688 -c "create catalog" -D /home/yashan/catalog

    create catalog successfully
    ```

2. List existing default configuration items.

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

    Use the following clear command to clear the compression algorithm configuration:

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
    -- Log in to the database as a regular user via *yasql* and execute the following operations
    CREATE TABLE age(a int);
    INSERT INTO age values(1);
    COMMIT;
    exit
    ```

5. Create a backup target tablespace.

    ```sql
    CREATE TABLESPACE TPS_TEST_SPC_BAK DATAFILE 'TPS_TEST_SPC_BAK1_1' SIZE 32M;
    COMMIT;
    exit
    ```

6. Perform a full backup of the tablespace.

    ```shell
    $ yasrman sys/********@192.168.1.2:1688 -c "backup tablespace TPS_TEST_SPC_BAK tag 'spc_full_1' format 'spc_full_bak1'" -D /home/yashan/catalog
    backup successfully
    ```

7. View the backup set.

    ```shell
    $ yasrman sys/********@192.168.1.2:1688 -c "list backup" -D /home/yashan/catalog

    Group: type TABLESPACE, tag: spc_full_1, format: /data/regress/ha_regress/ha_home/node_1/backup/spc_full_bak1, connect url: 127.0.0.    1:1601, nodeCount: 1, distribution: FALSE, isClient: FALSE, offset: 0
        backupset key: 628454824, base key: 0, backup path: /data/regress/ha_regress/ha_home/node_1/backup/spc_full_bak1 
    ```

8. Set the target tablespace to OFFLINE status.

    ```sql
    -- Preparation for restore, set target tablespace offline
    -- Log in to the database as a regular user via *yasql* and execute the following operations
    alter TABLESPACE TPS_TEST_SPC_BAK offline immediate;

    -- Query the tablespace and the corresponding data file status
   select id, status from v$tablespace where name='TPS_TEST_SPC_BAK';

            ID STATUS            
    ------------ ----------------- 
            6 OFFLINE          

    select status  from v$datafile where TS#=6;

    STATUS    
    --------- 
    RECOVER  
    exit;
    ```

9. RESTORE the target tablespace.

    ```shell
    # Before executing restore, manually clean up any leftover data files from the tablespace or specify clean file in the RESTORE command
    $ yasrman sys/********@192.168.1.2:1688 -c "restore tablespace TPS_TEST_SPC_BAK from tag 'spc_full_1'  clean file" -D /home/yashan/catalog 

    restore successfully
    ```

10. RECOVER the target tablespace.

    ```shell
    # Executing recover operation will automatically search for matching archive files from the archive backup set or the database's default archive folder
    $ yasrman sys/********@192.168.1.2:1688 -c "recover tablespace TPS_TEST_SPC_BAK" -D /home/yashan/catalog 

    recover successfully

    -- Log in to the database as a regular user via *yasql* and execute the following operations
    -- Query the status of the corresponding data file from RECOVER to OFFLINE
    SQL> select status from v$datafile where TS#=6;

    STATUS    
    --------- 
    OFFLINE  
    ```

    Changing from RECOVER to OFFLINE indicates that consistency recovery is complete.

11. Set the target tablespace to ONLINE status.

    ```sql
    alter tablespace TPS_TEST_SPC_BAK online;

    -- Query the status of the corresponding tablespace and data file from OFFLINE to ONLINE
    select id, status from v$tablespace where name='TPS_TEST_SPC_BAK';

            ID STATUS            
    ------------ ----------------- 
            6 ONLINE           

    select status  from v$datafile where TS#=6;

    STATUS    
    --------- 
    ONLINE   
    ```

    The tablespace is available for normal use after being set to ONLINE.