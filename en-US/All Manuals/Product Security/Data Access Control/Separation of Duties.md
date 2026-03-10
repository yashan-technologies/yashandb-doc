The separation of powers refers to the division of database management privileges, allowing different management positions to perform different roles (privilege sets), ensuring that they each fulfill their responsibilities while limiting and supervising each other. This helps to avoid excessive concentration of management privileges and enhances the security and compliance of the database system.

## Basic Principles

The core objective of the separation of powers is to prevent the abuse of privileges or errors in operations through the separation of responsibilities.

When the separation of powers is not enabled, the built-in DBA role in YashanDB has almost all privileges (except SHUTDOWN).

When the separation of powers functionality is enabled, the privileges of the DBA role are constrained accordingly, forming a "separation of powers" system together with the built-in SECURITY_ADMIN and AUDIT_ADMIN roles:

- DBA: Manages daily database operations, such as log file management, backup and recovery, various DDL operations, etc. It no longer holds any privileges related to auditing or security management.

- SECURITY_ADMIN: Manages the database's security mechanisms, such as managing users, roles, and granting privileges to other accounts.

- AUDIT_ADMIN: Manages the database's auditing mechanism, such as creating audit policies, tracking and analyzing audit logs, and responsible for tracing the actions of other users, including the supervision of DBAs and security administrators.

## Configuring the Separation of Powers

The functionality toggle is controlled by the value of the ENABLE_SEPARATE_DUTY parameter. After YashanDB is installed, ENABLE_SEPARATE_DUTY = FALSE by default, which means the separation of powers functionality is turned off. This parameter requires a database restart to take effect.

It is recommended to plan and confirm whether to use the separation of powers mode before creating the database. If needed, it can be enabled during the database creation operation. It is not advisable to frequently switch the privilege mode. If it was not enabled during database creation but there is a subsequent need to enable it, the current privileges of existing users should be cleaned up and organized before enabling the separation of powers.

1. Log in to the database as a user with ALTER SYSTEM privilege.

2. Execute the ALTER SYSTEM SET PARAMETER statement to change the value of the ENABLE_SEPARATE_DUTY parameter.

    ```sql
    ALTER SYSTEM SET ENABLE_SEPARATE_DUTY=TRUE SCOPE=SPFILE;

    -- If in ISC Distributed Cluster Deployment, specify TYPE, possible values CN|DN|MN|ALL
    ALTER SYSTEM SET ENABLE_SEPARATE_DUTY=TRUE SCOPE=SPFILE TYPE=ALL;
    ```

3. Restart the database for the configuration to take effect.

## Using the Separation of Powers

After enabling the separation of powers, the system privileges of administrative users will change accordingly, but the privileges of ordinary users and the objects owned by those users will not be affected.

For example, a user named sales1 is created and granted the DBA role. Another user named sales2 is created and granted the CONNECT and RESOURCE roles. User sales2 creates tables area1 and area2 and grants sales1 the object privilege to query area1.

In the default case where the separation of powers is not enabled, sales1, as a DBA role, can access any table under sales2.

```sql
-- Create users and grant privileges
CREATE USER sales1 IDENTIFIED BY "sales1";
CREATE USER sales2 IDENTIFIED BY "sales2";
GRANT DBA TO sales1;
GRANT CONNECT,RESOURCE TO sales2;

-- Ordinary user sales2
conn sales2/sales2
CREATE TABLE area1(C1 INT);
INSERT INTO area1 VALUES(1);
CREATE TABLE area2(C2 INT);
INSERT INTO area2 VALUES(2);
GRANT SELECT ON area1 TO sales1;

-- The DBA user sales1 can access any table under sales2, including the unauthorized area2
conn sales1/sales1
SELECT C1 FROM sales2.area1;
          C1 
------------ 
           1
SELECT C2 FROM sales2.area2;
          C2 
------------ 
           2
```

After enabling the separation of powers and restarting the database for it to take effect:

```sql
ALTER SYSTEM SET ENABLE_SEPARATE_DUTY=true SCOPE=SPFILE;
```

At this point, the system privileges of sales1 as a DBA role change:

- They can no longer access the unauthorized object area2.

- They cannot perform operations to grant system privileges.

However, the privileges of ordinary user sales2 remain unaffected and they can still manage the access privileges of their own resources (objects), such as granting query privileges on area2 to sales1.

```sql
-- The DBA user sales1 can access the authorized area1 but cannot access area2
conn sales1/sales1
SELECT C1 FROM sales2.area1;
          C1 
------------ 
           1
SELECT C2 FROM sales2.area2;
[1:15]YAS-02012 table or view does not exist

GRANT CREATE TABLE TO sales2;
YAS-02213 insufficient privileges

-- The ordinary user sales2 can still control the access privileges of their own table area2
conn sales2/sales2
GRANT SELECT ON area2 TO sales1;

-- After being granted access, the DBA user sales1 can access area2
conn sales1/sales1
SELECT C2 FROM sales2.area2;
          C2 
------------ 
           2
```
