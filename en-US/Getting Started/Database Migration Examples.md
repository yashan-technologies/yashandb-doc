For heterogeneous database migration, YashanDB supports the following two migration methods.

- Data Migration Platform - YMP: YMP (YashanDB Migration Platform) is a database migration product provided by YashanDB, supporting data source types including Oracle, MySQL, DM, and YashanDB, allowing for one-click migration of metadata and data to YashanDB. For specific compatible versions, please refer to [YMP Product Specifications](https://doc.yashandb.com/ymp/23.5/en/Product-Introduction/Specifications.html).
- Third-party migration platform: Migration to YashanDB from Oracle, MySQL, DM, etc., can be performed using third-party tools such as Kettle. For related operations, please refer to the knowledge base [Kettle Migrate MySQL to YashanDB](https://yashandb.com/newsinfo/7396987.html) and [Kettle Migrate PostgreSQL to YashanDB](https://yashandb.com/newsinfo/7396988.html).

This section will use YMP to migrate a small-scale Oracle database to YashanDB as an example to illustrate the complete migration process. During the migration, YMP will temporarily store and process business data. The larger the amount of data to migrate and the higher the speed requirements, the greater the resource requirements for the server on which YMP is deployed. Therefore, for this case as a migration example, it is recommended that the Oracle source database contains no more than 30 tables and that the data volume per table is limited to the order of one million. For data migration in a production environment, please refer to the [YMP Data Migration Platform Product Document](https://doc.yashandb.com/ymp/23.5/en/Product-Introduction/Summarize.html) for operations.

## Pre-migration Preparation

### YMP Server Configuration Requirements

Please prepare the YMP server environment according to the following requirements. If the YashanDB server resources are sufficient, YMP can be used on the same server as the database.

|Configuration Item |Minimum Requirement |Description |
| :----------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| Operating System    | CentOS 7.6 or above, KylinOS V10                             |                                                             |
| Processor Architecture | X86-64, ARM-64                                            | This section takes X86-64 processor as an example.          |
| CPU Cores           | 4 cores or more                                             |                                                             |
| Available Memory    | 8G or more                                                  |                                                             |
| Disk Space          | SSD, available size based on the size of the migration tables | Recommended to be no less than three times the maximum data volume of a single table in the tables to be migrated. |
| Open Ports          | 8090-8094                                                   | Minimum port requirements can be opened, or control can be managed by turning off the firewall or configuring the firewall whitelist. |
| JDK                 | JDK8, JDK11, JDK17                                         | 1. YMP only supports installation and use in environments with JDK8, JDK11, or JDK17. </br> 2. For ARM-64 architecture, the required JDK version for YMP must be JDK11 or above, to effectively resolve potential slow database connection issues. |
| openssl             | 1.1.1                                                       | The openssl version on the YMP server and the target YashanDB server must match. This requirement can be ignored if YMP and YashanDB are set up together. |
| Browser             | Google Chrome 88 or above; </br> Microsoft Edge 88 or above; </br> Firefox 78 or above |                                                             |

### Source Database Information Collection and Preparation

The following information needs to be collected for the source database.

- Server IP and port.
- Database service name.
- Login username and password.
- The login user for the source database must have CONNECT and RESOURCE privilege for the schema to be migrated.

This example considers only migrating the business data of a single schema from the Oracle database, and the objects within this schema must not have dependencies on objects in other schemas. For full migration of the Oracle database, please refer to the documentation on [Data Migration Platform - YMP](https://doc.yashandb.com/ymp/23.5/en/Product-Introduction/Summarize.html) for relevant privilege requirements.

### Target Database Information Collection and Configuration

The following information needs to be collected for the target YashanDB. The example information used in this section is from the configuration example completed in the [Quick Start with YashanDB](Quick Start with YashanDB) section, and relevant parameters must be adjusted according to the real environment during actual migration.

- The YashanDB 23.4 version supports compatibility with mysql mode. If migrating from MySQL to YashanDB, it is necessary to.
- Server IP and port.
- Database name and Schema name.
- Login username and password; this connecting user must have DBA privilege. The test user sales was created and granted privileges in the [Quick Start with YashanDB](Quick Start with YashanDB) section.

## Deploy YMP

1. Check the environment configuration and open the service ports.

   ```bash
   # firewall-cmd --zone=public --add-port=8090-8094/tcp --permanent
   ```

2. Configure the JDK installation path and information in the server environment variables; taking the installation path as /usr/tools/jdk8 as an example.

   ```bash
   # vi /etc/profile
   # export JAVA_HOME=/usr/tools/jdk8
   # export PATH=$JAVA_HOME/bin:$PATH
   # export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
   # source /etc/profile
   # java -version
   ```

3. Create the YMP installation user and grant privileges. When YMP is co-located with the database server, a different installation user needs to be created.

   ```bash
   # useradd -d /home/ymp -m ymp
   # passwd ymp
   ```

4. As the user ymp, upload the database installation package deployed in the [Quick Start with YashanDB](Quick Start with YashanDB) and the YMP installation package yashan-migrate-platform-{version_number}-linux-x86-64.zip to the /home/ymp path. After unzipping, execute the installation command.

   ```bash
   # su - ymp
   $ unzip yashan-migrate-platform-{version_number}-linux-x86-64.zip
   $ cd /home/ymp/yashan-migrate-platform/
   $ sh bin/ymp.sh install --db /home/ymp/yashandb-{version_number}-linux-x86_64.tar.gz
   $ sh bin/ymp.sh status
   YMP is running, pid is 7747.
   Built-in database is used, pid is 7617.
   ```

After deployment, the YMP service page can be accessed via http://YMP_IP:PORT/, where PORT defaults to 8090. Upon first login, you need to reset the login password. The initial username and password are admin/admin.

## Begin Migration

1. Select the [Data Source Management] page, click the [Add Data Source] button, and sequentially add the source Oracle database and target YashanDB as data sources. The connection configuration for Oracle must check the option **Is it a regular user**, and the configuration for YashanDB can use the test user sales created in the [Quick Start with YashanDB](Quick Start with YashanDB) chapter.
2. Select the [Task Management] page, click the [Create Task] button, and follow the prompts to select [Assessment], [Migration], and [Verification] to experience the complete process. Choose **Online Assessment** for the assessment type, configure the source and target databases, then enter the [Migration Task] configuration page. Complete the configuration as prompted and proceed with data migration. After migration, perform a verification to confirm data consistency. Detailed migration operations can refer to [YMP Quick Start](https://doc.yashandb.com/ymp/23.3/en/Quick-Start/Quick-Start.html).
