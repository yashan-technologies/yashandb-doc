The separation of duties refers to the division of database management privileges, allowing different management positions to perform different roles (privilege sets), ensuring that they each fulfill their responsibilities while limiting and supervising each other. This helps to avoid excessive concentration of management privileges and enhances the security and compliance of the database system.

## Basic Principles

The core objective of the separation of duties is to prevent the abuse of privileges or errors in operations through the separation of responsibilities.

If the three-separation mechanism is enabled, the permissions of the built-in SYS user and DBA role will be constrained accordingly, forming a "three-separation" system with the built-in SECURITOR and AUDITOR users. Additionally, it enforces stricter constraints on authorization operations and password policies.

| Constraint Item |  When Disabled | When Enabled |
|--------------------|----------------|----------------------|
| Built-in user SYS | Has all permissions | Only has system administrator permissions and no longer has audit or security management-related permissions. See the table below for details. |
| Built-in role DBA | Has all permissions except SHUTDOWN, backup, and recovery | Only has system administrator permissions. See the table below for details. |
| Authorization operation | - | Cannot grant the DBA, SECURITY_ADMIN, or AUDIT_ADMIN roles to regular users. |
| Password policies | - | Password lifecycle cannot exceed 7 days. <br/> Maximum consecutive failed login attempts cannot exceed 5. <br/>Password grace period is fixed at 0. |



If the separation of duties is enabled, the built-in users and permissions are as follows:

| Administrator Role | User | Permission Description |
|--------------------|----------------|----------------------|
| System Administrator | Built-in: SYS<br/>Custom: Users granted the DBA role before enabling the separation of duties | ALTER DATABASE<br/>ALTER SYSTEM<br/>CREATE/ALTER SESSION<br/>CREATE/ALTER/DROP USER<br/>CREATE/ALTER/DROP (ANY) ROLE<br />CREATE/ALTER/DROP/UNLIMITED TABLESPACE<br/>CREATE TABLE<br/>CREATE TYPE <br />CREATE/ALTER/DROP DATABASE LINK<br />CREATE/ALTER/DROP PUBLIC DATABASE LINK<br />CREATE LIBRARY<br />CREATE SEQUENCE<br/>CREATE SYNONYM<br/>CREATE VIEW<br/>CREATE PROCEDURE<br/>CREATE TRIGGER<br/>CREATE/ALTER/DROP ANY OUTLINE<br/>CREATE/ALTER/DROP PROFILE<br/>CREATE/ALTER/DROP ANY MATERIALIZED VIEW<br/>CREATE/DROP ANY DIRECTORY<br/>CREATE/DROP ANY CONTEXT<br/>ANALYZE ANY<br />YSTREAM_CAPTURE<br />FILE<br />SELECT FROM SYS.OBJECT |
| Security Administrator | Built-in: SECURITOR<br/>Custom: Users granted the SECURITY_ADMIN role before enabling the separation of duties | CREATE SESSION<br/>GRANT ANY PRIVILEGE/OBJECT PRIVILEGE/ROLE<br/>SELECT ON SYS.USERAUTH$ (View permission system table) <br/>ALL PRIVILEGES ON SYS.ANON_POLICY$  (Dynamic data masking policy)<br/>ADMINISTER KEY MANAGEMENT<br/>LBAC_DBA role (Row-level access control related permissions)<br/>SELECT_CATALOG_ROLE role |
| Audit Administrator | Built-in: AUDITOR<br/>Custom: Users granted the AUDIT_ADMIN role before enabling the separation of duties | CREATE SESSION<br/>AUDIT SYSTEM<br/>SELECT_CATALOG_ROLE role |



It is recommended to plan and confirm whether to use the separation of duties mode before creating the database. If needed, it can be enabled during the database creation operation. It is not advisable to frequently switch the privilege mode. If it was not enabled during database creation but there is a subsequent need to enable it, the current privileges of existing users should be cleaned up and organized before enabling the separation of duties.

## Configuring the Separation of Duties

The functionality toggle is controlled by the value of the ENABLE_SEPARATE_DUTY parameter. After YashanDB is installed, ENABLE_SEPARATE_DUTY = FALSE by default, which means the separation of duties functionality is turned off. This parameter requires a database restart to take effect.

1. Log in to the database as a user with ALTER SYSTEM privilege.

2. Execute the ALTER SYSTEM SET PARAMETER statement to change the value of the ENABLE_SEPARATE_DUTY parameter.

    ::: tabs
    == Standalone Deployment
```sql
ALTER SYSTEM SET ENABLE_SEPARATE_DUTY=TRUE SCOPE=SPFILE;
```
    == YAC/Distributed Cluster Deployment

In YAC/Distributed Cluster Deployment, the statement needs to be executed separately on each instance.

```sql
ALTER SYSTEM SET ENABLE_SEPARATE_DUTY=TRUE SCOPE=SPFILE;
```
    == ISC Distributed Cluster Deployment

```sql
ALTER SYSTEM SET ENABLE_SEPARATE_DUTY=TRUE SCOPE=SPFILE TYPE=ALL;
```
    :::

3. Restart the database for the configuration to take effect.

## Using the Separation of Duties

After enabling the separation of duties, the system privileges of administrative users will change accordingly, but the privileges of regular users and the objects owned by those users will not be affected.

For example, a user named sales1 is created and granted the DBA role. Another user named sales2 is created and granted the CONNECT and RESOURCE roles. User sales2 creates tables area1 and area2 and grants sales1 the object privilege to query area1.

In the default case where the separation of duties is not enabled, sales1, as a DBA role, can access any table under sales2.

```sql
-- Create users and grant privileges
CREATE USER sales1 IDENTIFIED BY "sales1";
CREATE USER sales2 IDENTIFIED BY "sales2";
GRANT DBA TO sales1;
GRANT CONNECT,RESOURCE TO sales2;

-- Regular user sales2
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

After enabling the separation of duties and restarting the database for it to take effect:

```sql
ALTER SYSTEM SET ENABLE_SEPARATE_DUTY=true SCOPE=SPFILE;
```

At this point, the system privileges of sales1 as a DBA role change:

- They can no longer access the unauthorized object area2.

- They cannot perform operations to grant system privileges.

However, the privileges of regular user sales2 remain unaffected and they can still manage the access privileges of their own resources (objects), such as granting query privileges on area2 to sales1.

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

-- The regular user sales2 can still control the access privileges of their own table area2
conn sales2/sales2
GRANT SELECT ON area2 TO sales1;

-- After being granted access, the DBA user sales1 can access area2
conn sales1/sales1
SELECT C2 FROM sales2.area2;
          C2 
------------ 
           2
```
