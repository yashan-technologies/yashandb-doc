
For the link from YashanDB to Oracle database, the system has the following prerequisites:

- The dblink plugin has been installed on the YashanDB server (you can confirm this by checking if the dblink folder exists under the $YASDB_HOME/third path).

- The Oracle Instant Client has been downloaded and installed on the YashanDB server.

   In environments where Oracle Instant Client is not installed, attempting to initiate a remote link to the Oracle database via DBLINK will throw an error due to the lack of necessary components.

- The libaio library has been installed on the YashanDB server.

   If the YashanDB server lacks the libaio library, when initiating the first remote link to the Oracle database via DBLINK, the yex_server sandbox process may experience a core dump while loading the driver (the yasdb process is unaffected). This error only affects the yex_server process during the first link attempt; the system can automatically recover to normal on subsequent link attempts.

Given the above requirements, administrators should take the necessary actions according to the following guidelines for databases that may use YashanDB -> Oracle remote links.

## Download and Install Oracle Instant Client

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

## Install libaio Library

1. Log in to the database server as the YashanDB installation user (e.g., yashan).

2. Take Centos as an example and install the libaio library using the following command:

   ```shell
   yum install libaio
   ```

## Create DBLINK and Perform Operations

1. Connect to the database using the test user sales through the database client tool *yasql*, with the default connection port being 1688.

   ```cmd
   > yasql sales/********@192.168.1.2:1688
   YashanDB SQL Enterprise Edition Release {version_number} x86_64
   
   Connected to:
   YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
   
   SQL> 
   ```

2. Create a DBLINK. 

   Below is an example; you can also refer to [CREATE DATABASE LINK](../../SQL语句/CREATE DATABASE LINK) for detailed DBLINK link configurations.

   ```sql
   -- Database link between YashanDB and YashanDB
   CREATE DATABASE LINK dblink_yashan CONNECT TO sales IDENTIFIED BY sales USING '192.168.1.2:1688';
   
   -- Database link between YashanDB and Oracle, visible to all users
   CREATE PUBLIC DATABASE LINK dblink_oracle CONNECT TO REGRESS IDENTIFIED BY REGRESS USING 'oracle:192.168.1.2:1521/orcl';
   ```

3. As an example of using DBLINK to link to a remote Oracle database and perform data operations.

   ```sql
   -- The following is the table definition of the department table on the remote Oracle
   desc department;
   
   Name                                      Null?    Type
   ----------------------------------------- -------- ----------------------
   DEPARTMENT_ID                                      NUMBER
   FIRST_NAME                                         VARCHAR2(20)
      
   -- Query the department table on YashanDB;
   SELECT * FROM department@dblink_oracle;
   DEPARTMENT_ID           DEPARTMENT_NAME          
   ----------------------- ------------------------------
   00001                   develop
   
   -- Insert data into the remote department table from YashanDB;
   INSERT INTO department@dblink_oracle VALUES(00002,test);
   
   SELECT * FROM department@dblink_oracle;
   DEPARTMENT_ID           DEPARTMENT_NAME          
   ----------------------- ------------------------------
   00001                   develop
   00002                   test
   
   ```
   
   The maximum number of DBLINK connections that can be concurrently opened by the database service is controlled by the system parameter [DBLINK_CURSOR_COUNT](../../../../参考手册/配置参数.html#dblinkcursorcount). The default value of this parameter is 32, and it can be adjusted according to specific business requirements.
   
   A DBLINK connection requests system idle memory to cache remote table business data. The system must satisfy the condition: Idle Memory > `DBLINK_CURSOR_COUNT` * 513K. If the system lacks sufficient memory when attempting to open a DBLINK again, the YAS-00101 error will be triggered.

4. Display the list of DBLINK created in the database service.

   ```sql
   SELECT * FROM DBA_DB_LINKS;
   ```

You can learn more about functionality and constraints through [dblink](../../通用SQL语法/dblink/dblink语法说明).



## FAQ

### YAS-07315 too many dblink result sets

When executing SQL statements involving dblink remote tables, if the error `YAS-07315: too many dblink result sets` occurs, it indicates that the number of open dblink remote tables has exceeded the limit configured by the system parameter `DBLINK_CURSOR_COUNT`. To resolve this issue, increase the parameter value appropriately after confirming sufficient memory availability on the database server.

```sql
-- 1.View system parameter configuration
show parameter dblink_cursor_count

NAME                                                             VALUE
---------------------------------------------------------------- ----------------------------------------------------------------
DBLINK_CURSOR_COUNT                                              32


-- 2.If the count of active dblink remote tables exceeds the configured system parameter threshold, an error message will be generated.
SELECT * FROM t1@dblink1 a1 CROSS JOIN t1@dblink1 a2 CROSS JOIN t1@dblink1 a3 CROSS JOIN t1@dblink1 a4 CROSS JOIN t1@dblink1 a5 CROSS JOIN t1@dblink1 a6 CROSS JOIN t1@dblink1 a7 CROSS JOIN t1@dblink1 a8 CROSS JOIN t1@dblink1 a9 CROSS JOIN t1@dblink1 a10 CROSS JOIN t1@dblink1 a11 CROSS JOIN t1@dblink1 a12 CROSS JOIN t1@dblink1 a13 CROSS JOIN t1@dblink1 a14 CROSS JOIN t1@dblink1 a15 CROSS JOIN t1@dblink1 a16 CROSS JOIN t1@dblink1 a17 CROSS JOIN t1@dblink1 a18 CROSS JOIN t1@dblink1 a19 CROSS JOIN t1@dblink1 a20 CROSS JOIN t1@dblink1 a21 CROSS JOIN t1@dblink1 a22 CROSS JOIN t1@dblink1 a23 CROSS JOIN t1@dblink1 a24 CROSS JOIN t1@dblink1 a25 CROSS JOIN t1@dblink1 a26 CROSS JOIN t1@dblink1 a27 CROSS JOIN t1@dblink1 a28 CROSS JOIN t1@dblink1 a29 CROSS JOIN t1@dblink1 a30 CROSS JOIN t1@dblink1 a31 CROSS JOIN t1@dblink1 a32 CROSS JOIN t1@dblink1 a33;

YAS-07315 too many dblink result sets

-- 3.Modify the system parameter
ALTER SYSTEM SET dblink_cursor_count=64 scope=spfile;


-- 4.Restart the database
-- 5.Confirm the system parameter
show parameter dblink_cursor_count

NAME                                                             VALUE
---------------------------------------------------------------- ----------------------------------------------------------------
DBLINK_CURSOR_COUNT                                              64


-- 6.Rerun the SQL statement
SELECT * FROM t1@dblink1 a1 CROSS JOIN t1@dblink1 a2 CROSS JOIN t1@dblink1 a3 CROSS JOIN t1@dblink1 a4 CROSS JOIN t1@dblink1 a5 CROSS JOIN t1@dblink1 a6 CROSS JOIN t1@dblink1 a7 CROSS JOIN t1@dblink1 a8 CROSS JOIN t1@dblink1 a9 CROSS JOIN t1@dblink1 a10 CROSS JOIN t1@dblink1 a11 CROSS JOIN t1@dblink1 a12 CROSS JOIN t1@dblink1 a13 CROSS JOIN t1@dblink1 a14 CROSS JOIN t1@dblink1 a15 CROSS JOIN t1@dblink1 a16 CROSS JOIN t1@dblink1 a17 CROSS JOIN t1@dblink1 a18 CROSS JOIN t1@dblink1 a19 CROSS JOIN t1@dblink1 a20 CROSS JOIN t1@dblink1 a21 CROSS JOIN t1@dblink1 a22 CROSS JOIN t1@dblink1 a23 CROSS JOIN t1@dblink1 a24 CROSS JOIN t1@dblink1 a25 CROSS JOIN t1@dblink1 a26 CROSS JOIN t1@dblink1 a27 CROSS JOIN t1@dblink1 a28 CROSS JOIN t1@dblink1 a29 CROSS JOIN t1@dblink1 a30 CROSS JOIN t1@dblink1 a31 CROSS JOIN t1@dblink1 a32 CROSS JOIN t1@dblink1 a33;

          C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3
------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- -----------

```

If the database service process reports an insufficient memory error during restart, you can either reduce the values of the parameter `DBLINK_CURSOR_COUNT`, or expand memory allocation, then attempt to restart the database service after implementing these changes.

### YAS-07314 too many connections for dblink %s

This error indicates that the number of accesses to remote tables via a single dblink has exceeded the system-configured limit. The limit is controlled by the `MAX_DBLINK_CONNS` parameter in the `yex_server.ini` configuration file, which accepts values in the range **[64, 16384]**. To resolve this issue, you can follow the guidelines in [Sandbox Process Management](./yex_server沙箱进程管理) to modify the parameter value. For more dblink-related sandbox process parameters, please refer to the [dblink](./dblink语法说明) documentation.


### YAS-07330 ERR\_YEX\_TOO\_MANY\_XACTS

This error indicates that the number of session openning dblink has exceeded the system-configured limit. The limit is controlled by the `AXS_MAX_XACTS` parameter in the `yex_server.ini` configuration file, which accepts values in the range [10244, 16384]. To resolve this issue, you can follow the guidelines in [Sandbox Process Management](./yex_server沙箱进程管理) to modify the parameter value. For more dblink-related sandbox process parameters, please refer to the [dblink](./dblink语法说明) documentation.

### YAS-07331 transaction branches in same session can not exceed %d

The maximum number of distinct DBLINKs that can be simultaneously opened within a session is limited to 32. This error indicates that the current session has exceeded this limit by attempting to open more than 32 DBLINKs concurrently.


