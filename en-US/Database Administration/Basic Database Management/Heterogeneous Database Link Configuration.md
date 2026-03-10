Heterogeneous database links refer to remote links (DBLINK) created from YashanDB to other non-YashanDB databases.

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
   YashanDB SQL Enterprise Edition Release {version number} x86_64
   
   Connected to:
   YashanDB Server Enterprise Edition Release {version number} x86_64 - Linux
   
   SQL> 
   ```

2. Create a DBLINK. Below is an example; you can also refer to [CREATE DATABASE LINK](../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/CREATE DATABASE LINK) for detailed DBLINK link configurations.

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

   You can learn more about functionality and constraints through [dblink](../../Development Guide/SQL Reference Manual/General SQL Syntax/dblink).