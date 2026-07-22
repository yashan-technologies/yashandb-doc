Data in the database and operations on the data should be isolated according to the different identities of users to avoid events such as data destruction and leakage.

To ensure the information security and stable operation of the database, the DBA should fully understand the privilege-related functionality provided by YashanDB. Based on the actual business of the enterprise, they should plan and build a reasonable privilege system to accurately grant users the minimum required privilege.

The privilege mechanism provided by YashanDB includes:

- privilege:

    - [System Privileges](System Privileges): System-level privilege control, including control of various object operation privileges for specified users.

    - [Object Privileges](Object Privileges): Object-level privilege control, including control of DML, DDL and other privileges for tables.

- [Roles](Roles): Includes system-built roles and user-defined roles.

    - Built-in (system pre-defined) roles: Roles preset during the database creation.

    - Custom roles: Roles created by the DBA based on business needs.

YashanDB provides a series of privilege-related views for administrators to perform privilege statistics and checks, such as:

- DBA_USERS: Displays information about all database users.

- DBA_ROLES: Displays information about all roles in the database.

- DBA_ROLE_PRIVS: Displays records of role grants.

- DBA_SYS_PRIVS: Displays records of system privilege grants.

- DBA_TAB_PRIVS: Displays records of object privilege grants.