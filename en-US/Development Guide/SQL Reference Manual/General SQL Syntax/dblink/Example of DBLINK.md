
For the link from YashanDB to Oracle database, the system has the following prerequisites:

- The DBLink plugin has been installed on the YashanDB server (you can confirm this by checking if the dblink folder exists under the $YASDB_HOME/third path).

- The Oracle Instant Client has been downloaded and installed on the YashanDB server.

   In environments where Oracle Instant Client is not installed, attempting to initiate a remote link to the Oracle database via DBLink will throw an error due to the lack of necessary components.

- The libaio library has been installed on the YashanDB server.

   If the YashanDB server lacks the libaio library, when initiating the first remote link to the Oracle database via DBLink, the yex_server sandbox process may experience a core dump while loading the driver (the yasdb process is unaffected). This error only affects the yex_server process during the first link attempt; the system can automatically recover to normal on subsequent link attempts.

For the link from YashanDB to DAMENG Database, the system has the following prerequisites:

- The DBLink plugin has been installed on the YashanDB server (you can confirm this by checking if the dblink_dm folder exists under the $YASDB_HOME/third path).

- The DAMENG Database OCI driver has been downloaded and installed on the YashanDB server.

   In environments where the DAMENG OCI driver is not installed, attempting to initiate a remote link to the DAMENG Database via DBLink will throw an error due to the lack of necessary components.

For the link from YashanDB to KingbaseES Database, the system has the following prerequisites:

- The DBLink plugin has been installed on the YashanDB server (you can confirm this by checking if the dblink-kingbase folder exists under the $YASDB_HOME/third path).

- The KingbaseES Database DCI driver has been downloaded and installed on the YashanDB server.

   In environments where the KingbaseES DCI driver is not installed, attempting to initiate a remote link to the KingbaseES Database via DBLink will throw an error due to the lack of necessary components.

Given the above requirements, administrators should take the necessary actions according to the following guidelines for databases that may use YashanDB -> heterogeneous database remote links.

## Install Heterogeneous Database Drivers

### Install Oracle Instant Client and libaio Library

1. Log in to the database server as the YashanDB installation user (e.g., yashan).

2. Download the corresponding Oracle Instant Client installation package from the [Oracle official website](https://www.oracle.com/database/technologies/instant-client/downloads.html) according to the YashanDB server environment.

   The OCI version requirements are as follows:

    - x86: 21 series

    - ARM: 19 series

3. Unpack the installation package to a local directory, for example, `/home/oracle-instant-client/`.

4. Set the dynamic library dependency path.

   ```shell
   $ vi ~/.bashrc

   # Add the following information to the file and save it
   export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/oracle-instant-client/lib
   ```

5. Apply the environment variables.

   ```shell
   $ source ~/.bashrc

   # Check in the echo output whether the above path already exists
   $ echo $LD_LIBRARY_PATH
   ```

6. Refresh the environment variables read by the running YashanDB. This is done by restarting the yasom process and all yasagent processes.

   If the database-related processes have not been started yet, there is no need to perform this operation.

   ```shell
   # Check if either the yasom process or the yasagent process exists. If so, it needs to be restarted.
   $ ps -ef | grep -E "yasom|yasagent" | grep -v grep

   # Restart the yasom process
   $ yasboot process yasom restart -c yashandb

   # Restart the yasagent process on all nodes
   $ yasboot process yasagent restart -c yashandb -t hosts.toml
   ```

7. Take Centos as an example and install the libaio library using the following command.

   ```shell
   yum install libaio
   ```

### Install DAMENG Database OCI Driver

1. Log in to the database server as the YashanDB installation user (e.g., yashan).

2. According to the YashanDB server environment, download the client installation package for the corresponding DM8 version from the DAMENG Database official website, and obtain the DAMENG OCI driver.

3. Unpack the installation package to a local directory, for example, `/home/dm-instant-client/`.

4. Set the dynamic library dependency path.

   ```shell
   $ vi ~/.bashrc

   # Add the following information to the file and save it
   export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/dm-instant-client/lib
   ```

5. Apply the environment variables.

   ```shell
   $ source ~/.bashrc

   # Check in the echo output whether the above path already exists
   $ echo $LD_LIBRARY_PATH
   ```

6. Refresh the environment variables read by the running YashanDB. This is done by restarting the yasom process and all yasagent processes.

   If the database-related processes have not been started yet, there is no need to perform this operation.

   ```shell
   # Check if either the yasom process or the yasagent process exists. If so, it needs to be restarted.
   $ ps -ef | grep -E "yasom|yasagent" | grep -v grep

   # Restart the yasom process
   $ yasboot process yasom restart -c yashandb

   # Restart the yasagent process on all nodes
   $ yasboot process yasagent restart -c yashandb -t hosts.toml
   ```

### Install KingbaseES Database DCI Driver

1. Log in to the database server as the YashanDB installation user (e.g., yashan).

2. According to the YashanDB server environment, download the DCI driver installation package for the corresponding KingbaseES V8 or later version from the KingbaseES Database official website, and obtain the KingbaseES DCI driver.

3. Unpack the installation package to a local directory, for example, `/home/kingbase-dci/`.

4. Set the dynamic library dependency path.

   ```shell
   $ vi ~/.bashrc

   # Add the following information to the file and save it
   export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/kingbase-dci/lib
   ```

5. Apply the environment variables.

   ```shell
   $ source ~/.bashrc

   # Check in the echo output whether the above path already exists
   $ echo $LD_LIBRARY_PATH
   ```

6. Refresh the environment variables read by the running YashanDB. This is done by restarting the yasom process and all yasagent processes.

   If the database-related processes have not been started yet, there is no need to perform this operation.

   ```shell
   # Check if either the yasom process or the yasagent process exists. If so, it needs to be restarted.
   $ ps -ef | grep -E "yasom|yasagent" | grep -v grep

   # Restart the yasom process
   $ yasboot process yasom restart -c yashandb

   # Restart the yasagent process on all nodes
   $ yasboot process yasagent restart -c yashandb -t hosts.toml
   ```

## Create DBLink and Perform Operations

1. Connect to the database using the test user sales through the database client tool *yasql*, with the default connection port being 1688.

   ```cmd
   yasql sales/********@192.168.1.2:1688
   YashanDB SQL Enterprise Edition Release {version_number} x86_64
   
   Connected to:
   YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
   
   SQL> 
   ```

2. Create a DBLink. 

   Below is an example; you can also refer to [CREATE DATABASE LINK](../../SQL Statements/CREATE DATABASE LINK) for detailed DBLink link configurations.

   ```sql
   -- Database link between YashanDB and YashanDB
   CREATE DATABASE LINK dblink_yashan CONNECT TO sales identified BY sales USING '192.168.1.2:1688';
   
   -- Database link between YashanDB and Oracle, visible to all users
   CREATE PUBLIC DATABASE LINK dblink_oracle CONNECT TO REGRESS identified BY REGRESS USING 'oracle:192.168.1.2:1521/orcl';

   -- Database link between YashanDB and DAMENG Database
   CREATE DATABASE LINK dblink_dm CONNECT TO DMUSER IDENTIFIED BY DMPWD USING 'dm:192.168.1.3:5236';

   -- Database link between YashanDB and KingbaseES Database
   CREATE DATABASE LINK dblink_king CONNECT TO KINGUSER IDENTIFIED BY KINGPWD USING 'king:192.168.1.4:54321/TEST';
   ```

3. As an example of using DBLink to link to a remote Oracle database and perform data operations.

   ```sql
   -- The following is the table definition of the department table on the remote Oracle
   desc department;
   
   Name                                      Null?    Type
   ----------------------------------------- -------- ----------------------
   DEPARTMENT_ID                                      NUMBER
   FIRST_NAME                                         VARCHAR2(20)
      
   -- Query the department table on YashanDB;
   select * from department@dblink_oracle;
   DEPARTMENT_ID           DEPARTMENT_NAME          
   ----------------------- ------------------------------
   00001                   develop
   
   -- Insert data into the remote department table from YashanDB;
   insert into department@dblink_oracle values(00002,test);
   
   select * from department@dblink_oracle;
   DEPARTMENT_ID           DEPARTMENT_NAME          
   ----------------------- ------------------------------
   00001                   develop
   00002                   test
   
   ```
   
   The maximum number of DBLink connections that can be concurrently opened by the database service is controlled by the system parameter [DBLINK_CURSOR_COUNT](../../../../Reference Manual/Configuration Parameters.md#dblinkcursorcount). The default value of this parameter is 32, and it can be adjusted according to specific business requirements.
   
   A DBLink connection requests system idle memory to cache remote table business data. The system must satisfy the condition: Idle Memory > `DBLINK_CURSOR_COUNT` * 513K. If the system lacks sufficient memory when attempting to open a DBLink again, the YAS-00101 error will be triggered.

4. Display the list of DBLink created in the database service.

   ```sql
   select * from DBA_DB_LINKS;
   ```

5. View all opened DBlinks and their transaction status in the current session.

   ```sql
   select * from V$DBLINK;
   ```

6. If it is confirmed that a DBLink opened in the current session is temporarily not needed again, it can be closed to release the corresponding resources.

   ```sql
   -- First commit or rollback the remote transactions in the corresponding DBLink as needed before closing the DBLink
   COMMIT;

   -- Method 1: Execute ALTER SESSION statement to close the target DBLink
   ALTER SESSION CLOSE DATABASE LINK dblink_oracle;

   -- Method 2: Call DBMS_SESSION advanced package to close the target DBLink
   exec DBMS_SESSION.CLOSE_DATABASE_LINK(dblink_oracle);
   ```

You can learn more about functionality and constraints through [Syntax Definition of DBLink](../../General SQL Syntax/dblink/Syntax Definition of DBLink).



## FAQ

### YAS-07315 too many dblink result sets

When executing SQL statements involving DBLink remote tables, if the error `YAS-07315: too many dblink result sets` occurs, it indicates that the number of open DBLink remote tables has exceeded the limit configured by the system parameter `DBLINK_CURSOR_COUNT`. 

Solutions:

- **Solution 1**: After confirming that a DBLink is no longer needed, the target DBLink can be closed to release corresponding resources. Before closing, the corresponding transactions must be committed/rolled back.

   ```sql
   -- 1. Query DBLinks that are currently open in the session and whether they are in a transaction
   SELECT DB_LINK,IN_TRANSACTION FROM V$DBLINK;

   DB_LINK                                                          IN_TRANSACTION
   ---------------------------------------------------------------- --------------
   LinkToOra                                                        YES
   ……

   -- 2. First commit or rollback the remote transactions in the corresponding DBLink as needed before closing the DBLink
   COMMIT;

   -- 3. Confirm the transaction status of the target DBLink
   SELECT DB_LINK,IN_TRANSACTION FROM V$DBLINK WHERE DB_LINK = 'LinkToOra';

   DB_LINK                                                          IN_TRANSACTION
   ---------------------------------------------------------------- --------------
   LinkToOra                                                        NO

   -- 4. Close the target DBLink
   -- Method 1: Execute ALTER SESSION statement to close the target DBLink
   ALTER SESSION CLOSE DATABASE LINK dblink_oracle;

   -- Method 2: Call DBMS_SESSION advanced package to close the target DBLink
   exec DBMS_SESSION.CLOSE_DATABASE_LINK(dblink_oracle);
   ```

- **Solution 2**: If it is confirmed that the database server has sufficient memory, the `DBLINK_CURSOR_COUNT` parameter value can be appropriately increased.

   ```sql
   -- 1.View system parameter configuration
   show parameter DBLINK_CURSOR_COUNT

   NAME                                                             VALUE
   ---------------------------------------------------------------- ----------------------------------------------------------------
   DBLINK_CURSOR_COUNT                                              32

   1 row fetched.

   -- 2.If the count of active DBLink remote tables exceeds the configured system parameter threshold, an error message will be generated.
   select * from t1@dblink1 a1 cross join t1@dblink1 a2 cross join t1@dblink1 a3 cross join t1@dblink1 a4 cross join t1@dblink1 a5 cross join t1@dblink1 a6 cross join t1@dblink1 a7 cross join t1@dblink1 a8 cross join t1@dblink1 a9 cross join t1@dblink1 a10 cross join t1@dblink1 a11 cross join t1@dblink1 a12 cross join t1@dblink1 a13 cross join t1@dblink1 a14 cross join t1@dblink1 a15 cross join t1@dblink1 a16 cross join t1@dblink1 a17 cross join t1@dblink1 a18 cross join t1@dblink1 a19 cross join t1@dblink1 a20 cross join t1@dblink1 a21 cross join t1@dblink1 a22 cross join t1@dblink1 a23 cross join t1@dblink1 a24 cross join t1@dblink1 a25 cross join t1@dblink1 a26 cross join t1@dblink1 a27 cross join t1@dblink1 a28 cross join t1@dblink1 a29 cross join t1@dblink1 a30 cross join t1@dblink1 a31 cross join t1@dblink1 a32 cross join t1@dblink1 a33;

   YAS-07315 too many dblink result sets

   -- 3.Modify the system parameter
   alter system set DBLINK_CURSOR_COUNT=64 scope=spfile;

   Succeed.

   -- 4.Restart the database
   -- 5.Confirm the system parameter
   show parameter DBLINK_CURSOR_COUNT

   NAME                                                             VALUE
   ---------------------------------------------------------------- ----------------------------------------------------------------
   DBLINK_CURSOR_COUNT                                              64

   1 row fetched.

   -- 6.Rerun the SQL statement
   select * from t1@dblink1 a1 cross join t1@dblink1 a2 cross join t1@dblink1 a3 cross join t1@dblink1 a4 cross join t1@dblink1 a5 cross join t1@dblink1 a6 cross join t1@dblink1 a7 cross join t1@dblink1 a8 cross join t1@dblink1 a9 cross join t1@dblink1 a10 cross join t1@dblink1 a11 cross join t1@dblink1 a12 cross join t1@dblink1 a13 cross join t1@dblink1 a14 cross join t1@dblink1 a15 cross join t1@dblink1 a16 cross join t1@dblink1 a17 cross join t1@dblink1 a18 cross join t1@dblink1 a19 cross join t1@dblink1 a20 cross join t1@dblink1 a21 cross join t1@dblink1 a22 cross join t1@dblink1 a23 cross join t1@dblink1 a24 cross join t1@dblink1 a25 cross join t1@dblink1 a26 cross join t1@dblink1 a27 cross join t1@dblink1 a28 cross join t1@dblink1 a29 cross join t1@dblink1 a30 cross join t1@dblink1 a31 cross join t1@dblink1 a32 cross join t1@dblink1 a33;

            C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3
   ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- -----------

   0 rows fetched.
   ```

If the database service process reports an insufficient memory error during restart, you can either reduce the values of the parameter `DBLINK_CURSOR_COUNT` by adjusting configurations in [Configuration File Adjustment](../../../../Database Administration/Storage Management/Database File Management/Configuration Parameter File and Password File Management), or expand memory allocation, then attempt to restart the database service after implementing these changes.

### YAS-07314 too many connections for dblink %s

This error indicates that the number of accesses to remote tables via a single DBLink has exceeded the system-configured limit. The limit is controlled by the `MAX_DBLINK_CONNS` parameter in the `yex_server.ini` configuration file, which accepts values in the range **[64, 16384]**. To resolve this issue, you can follow the guidelines in [Sandbox Process Management](./yex_server Sandbox Process Management) to modify the parameter value. For more DBLink-related sandbox process parameters, please refer to [Syntax Definition of DBLink](./Syntax Definition of DBLink).

### YAS-07318 too many db links

This error indicates that the number of DBLink objects created and currently in use exceeds the system configuration limit. This limit is controlled by the [MAX_DBLINK_OBJECTS](./Syntax Definition of DBLink.md#max_dblink_objects) parameter in the yex_server.ini file, with a value range of [1024, 16384]. To adjust this parameter, refer to [yex_server sandbox process management](./yex_server Sandbox Process Management) for the corresponding operation.

### YAS-07330 ERR_YEX_TOO_MANY_XACTS

This error indicates that the number of session openning DBLink has exceeded the system-configured limit. The limit is controlled by the `AXS_MAX_XACTS` parameter in the `yex_server.ini` configuration file, which accepts values in the range [10244, 16384]. To resolve this issue, you can follow the guidelines in [Sandbox Process Management](./yex_server Sandbox Process Management) to modify the parameter value. For more DBLink-related sandbox process parameters, please refer to [Syntax Definition of DBLink](./Syntax Definition of DBLink).

### YAS-07331 transaction branches in same session can not exceed %d

The maximum number of distinct DBLinks that can be simultaneously opened within a session is limited to 32. This error indicates that the current session has exceeded this limit by attempting to open more than 32 DBLinks concurrently.

If it is confirmed that a DBLink is temporarily not needed, it can be closed (by executing [ALTER SESSION](../../SQL Statements/ALTER SESSION.md#closedblink) statement or [DBMS_SESSION.CLOSE_DATABASE_LINK](../../../PL Reference Manual/Built-in Advanced PL Packages/DBMS_SESSION.md#closedblink) stored procedure) after committing/rolling back its corresponding transactions to release the corresponding resources.


