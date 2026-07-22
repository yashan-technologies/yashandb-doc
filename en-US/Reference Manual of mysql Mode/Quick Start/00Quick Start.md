Starting from version V23.4, YashanDB supports two SQL syntax modes: yashan and mysql. It has achieved compatibility with MySQL 5.7 syntax. Users who are familiar with MySQL database products can quickly experience the database functionality of YashanDB in mysql mode through this chapter.

In mysql mode, we have primarily implemented the following product functionalities:
1. 100% compatibility with the complete set of commonly used MySQL 5.7 protocol commands, data types, and information_schema/MySQL schema/performance_schema system views, compatible with MySQL 5.7 syntax;
2. Support for native MySQL tools and clients, including mysql-jdbc5.1.40, mysqldump10.13, mysql14.14, mysql_client5.7.42, and mydumperv0.16.9-1, to connect directly to YashanDB.