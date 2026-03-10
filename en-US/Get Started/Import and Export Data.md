YashanDB has built-in data import and export tools, which support importing CSV format files exported from a single table of other databases into YashanDB. It also allows importing metadata files or data files exported from YashanDB into another instance of YashanDB.

This section will describe the operation steps for the following two scenarios for data import and export. You may also refer to the corresponding documentation for detailed operational instructions on [yasldr](../All Manuals/Tools Guide/yasldr/00yasldr), [imp](../All Manuals/Tools Guide/imp/00imp), and [exp](../All Manuals/Tools Guide/exp/00exp).

- Importing CSV format data files into YashanDB.
- Exporting and importing metadata between YashanDB instances.

## Importing CSV Data Files

1. Log in to the database server as the installation user, and execute the following command in the HOME path to create the `datafile` file. Simulate writing test data in CSV format, press **Esc**, and input `:wq` to save and exit file editing:

   ```bash
   $ vi datafile
   
   1|load|101
   2|load|201
   ```

2. Connect to the database using the test user `sales` through the database client tool *yasql*, with the default connection port of 1688.

   ```shell
   $ yasql sales/********@192.168.1.2:1688
   YashanDB SQL Enterprise Edition Release {version_number} x86_64
   
   Connected to:
   YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
   
   SQL> 
   ```

3. Prepare the target table for data file import. In this document, we create a table named `loadData` as an example:

   ```sql
   CREATE TABLE loadData (c1 INT,c2 CHAR(10),c3 INT);
   ```

4. As the database installation user, execute the shell command on the database server to import data from the `datafile` file into the `loadData` table of the `yasldr_user` user:

   ```shell
   $ yasldr yasldr_user/yasldr batch_size=4032 control_text="'LOAD DATA OPTIONS(DEGREE_OF_PARALLELISM=2) INFILE '/home/yashan/datafile' FIELDS TERMINATED BY '|' OPTIONALLY ENCLOSED BY '\"' APPEND INTO TABLE loadData(c1,c2,c3) '"
   
   YashanDB Loader Enterprise Edition Release {version_number} x86_64 297f388
   2 rows successfully loaded.
   Check /home/yashan/datafile.log for more info.
   [YASLDR] execute succeeded
   ```

   The above command separates the data in the `datafile` file by `|` and imports it into the `c1`, `c2`, and `c3` columns of the `loadData` table.

5. Reconnect to the database using the test user `sales` through the database client tool *yasql* to verify the data import:

   ```shell
   $ yasql sales/********@192.168.1.2:1688
   YashanDB SQL Enterprise Edition Release {version_number} x86_64
   
   Connected to:
   YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

   SQL> SELECT c1,c2,c3 FROM loadData;
      
             C1 C2                      C3
   ------------ ------------- ------------
              1 load                   101
              2 load                   201
   ```

## Metadata Import and Export Between YashanDB

The *exp* tool is an accompanying export tool for YashanDB, providing capabilities for metadata export and CSV export; the *imp* tool is an accompanying import tool for YashanDB, offering capabilities for metadata import.

During actual business development, you may need to import the metadata objects completed in YashanDB into the production environment when going live. This section will describe how to perform metadata import and export operations between YashanDB instances.

### Exporting Metadata

1. Connect to the source database using the test user `sales` through the database client tool *yasql*, with the default connection port of 1688. You may also recreate the user and grant DBA privilege for connection.

   ```shell
   $ yasql sales/********@192.168.1.2:1688
   YashanDB SQL Enterprise Edition Release {version_number} x86_64
   
   Connected to:
   YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
   
   SQL> 
   ```

2. Create sample tables and insert data:

   ```sql
   CREATE TABLE classmate_info(c1 INT,c2 CHAR(10));
   INSERT INTO classmate_info VALUES(1,'h'),(2,'a'),(3,'c');
   CREATE TABLE classmate_info1(c1 INT,c2 INT);
   INSERT INTO classmate_info1 VALUES(1,2),(3,4),(5,6);
   COMMIT;
   ```

3. Log in to the database server as the installation user, and execute the following command to export all metadata under the `sales` schema to the `export.owner.export` file. Please replace `********` with the password set for the sys user:

   ```shell
   $ exp sales/******** FILE=export.owner.export OWNER=sales
   
   YashanDB Export Release {version_number} x86_64 297f388
   Start export user [SALES] from dba view
   Exporting tablespaces...
   Exporting database links...
   Exporting sequences...
   Exporting object synonyms...
   Exporting type dependencies synonyms...
   Exporting types...
   Exporting tables...
      exporting table IMPORT_USER.CLASSMATE_INFO   3 rows exported
      exporting table IMPORT_USER.CLASSMATE_INFO1          3 rows exported
   Exporting access constraints...
   Exporting indexes...
   Exporting other constraints...
   Exporting primary keys...
   Exporting foreign keys...
   Exporting other objects...
   export terminated successfully
   ```

4. Execute the following command to view the exported metadata file:

   ```shell
   $ ll
   
   total 24
   -rw-r-----  1 yashan yashan 16771 Aug 14 21:08 export.owner.export
   drwxrwxr-x 12 yashan yashan   259 Aug  8 16:33 install
   drwxr-----  3 yashan yashan    20 Aug  8 16:45 yasdb_data
   drwxrwxr-x  3 yashan yashan    22 Aug  8 16:41 yasdb_home
   ```

### Importing Metadata

For the convenience of demonstration, we continue to use the database created in [Quick Start with YashanDB](Quick Start with YashanDB) as an example to demonstrate the metadata import operation. In actual usage, if the target database is different from the source database, you need to create users on the target database and grant DBA privilege.

1. Execute the following command to connect to YashanDB:

   ```bash
   $ yasql sales/********
   YashanDB SQL Enterprise Edition Release {version_number} x86_64
   
   Connected to:
   YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
   
   SQL> 
   ```

2. Execute the following SQL statements to delete the `classmate_info` and `classmate_info1` tables:

   ```sql
   DROP TABLE classmate_info;
   DROP TABLE classmate_info1;
   ```
   
3. Query the `USER_TABLES` view to check the table information for the current user. At this point, there are no tables under the `sales` user:

   ```sql
   SELECT table_name FROM USER_TABLES;
   
   TABLE_NAME
   ----------------------------------------------------------------

   ```

4. Log in to the database server as the installation user, and execute the following command to import the metadata file into the `sales` user. Please replace `********` with the password set for the user:

   ```shell
   $ imp sys/******** FILE=export.owner.export FROMUSER=sales
   
   YashanDB Import Release {version_number} x86_64 297f388
   Start import User [SALES] from file export.owner.export
   Checking tablespace...
   Switch to owner SALES
   Importing table CLASSMATE_INFO        3 rows imported
   Importing table CLASSMATE_INFO1       3 rows imported
   import terminated successfully
   ```

5. Log in to the database and check the user table information:

   ```bash
   $ yasql sales/********
   YashanDB SQL Enterprise Edition Release {version_number} x86_64
   
   Connected to:
   YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
   
   SQL> SELECT table_name FROM USER_TABLES;
   
   TABLE_NAME
   ----------------------------------------------------------------
   classmate_info1
   classmate_info
   ```

6. Execute the following SQL statements to view the data in the `classmate_info` and `classmate_info1` tables:

   ```sql
   SELECT c1,c2 FROM classmate_info;
   
             C1 C2
   ------------ -------------
              1 h
              2 a
              3 c
   
   SELECT c1,c2 FROM classmate_info1;
   
             C1           C2
   ------------ ------------
              1            2
              3            4
              5            6
   ```
