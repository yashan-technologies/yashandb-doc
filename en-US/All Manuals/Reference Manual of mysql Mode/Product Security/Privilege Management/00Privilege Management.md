Data in the database and operations on the data should be isolated according to the different identities of users to avoid incidents such as data destruction and leakage.

To ensure the information security and stable operation of the database, the DBA should have a thorough understanding of the privilege-related functionality provided by YashanDB, and plan and build a reasonable privilege system in accordance with the actual business of the enterprise, accurately granting users the minimum required privileges.

The privilege mechanism provided by YashanDB (mysql mode) includes:

- [System Privileges](System Privileges): Global system-level privilege control, including control over operation privileges for various objects under specified users.

- [Schema Privileges](Schema Privileges): Database (schema) level privilege control, which affects the privilege control over all objects under a specific database, including privileges for creating and deleting tables and accessing tables within the corresponding database.

- [Object Privileges](Object Privileges): Object-level (table) privilege control, including operation privilege control for tables.

YashanDB (mysql mode) provides a series of privilege-related views for administrators to perform privilege statistics and checks, such as:

- MYSQL.USER, MYSQL.DB, or INFORMATION_SCHEMA.USER_PRIVILEGES views: Display information about all authorized system-level privileges in the current system.

- INFORMATION_SCHEMA.SCHEMA_PRIVILEGES view: Displays information about all authorized schema-level privileges in the current system.

- MYSQL.TABLES_PRIV or INFORMATION_SCHEMA.TABLE_PRIVILEGES views: Display information about all authorized table-level object privileges in the current system.

For users created in mysql mode, it may be necessary to consider whether they need to be granted the corresponding [privileges in yashan mode](../../../Product Security/Data Access Control/Privilege and Role Management/00Privilege and Role Management).