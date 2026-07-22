## Role-Based Access Control
YashanDB adopts RBAC (Role-Based Access Control) to restrict authorized users' access to the system.

Roles contain a set of privileges, which effectively manage privileges through roles. Administrators only need to focus on users' roles, while for roles, the focus is only on their privilege set.

**Separation of Duties**

During the operation of the database, continuous maintenance and management are required. Theoretically, the sys user can perform all management operations, but this poses significant security risks:

- sys is the super administrator account of YashanDB, possessing complete privileges, able to execute all operations on the database, which may lead to severe failures due to operational mistakes.

- Actions performed using the sys account are all logged or recorded as sys, making it impossible to track back to the actual executor.

- If the sys account is used for daily management of the database and is accessed too frequently, it easily becomes a target for hackers to steal passwords. Once the password is successfully stolen, the entire database system may be exposed, resulting in serious consequences.

Therefore, after the product installation is completed and the system administrator takes over the management of the database, it is recommended to first create a database administrator account and grant it corresponding management privileges based on management responsibilities. Subsequent management and operational tasks should be performed using this administrator account.

In the requirements of the computer information system's Classified Protection (level 3 and above standards), it is stated that database systems must adopt a "Separation of Duties" privilege system, as shown in the figure below:

![](./image/3privs.png)

The separation of duties divides database management privileges, allowing various administrators to exercise power independently while also restraining each other, effectively avoiding the risk of excessive concentration of management privileges.

In YashanDB, the separation of duties is controlled by the ENABLE_SEPARATE_DUTY configuration parameter, and users can choose whether to enable it based on security requirements.  



If the separation of duties is enabled, the built-in users and permissions are as follows:

| Administrator Role | User | Permission Description |
|--------------------|----------------|----------------------|
| System Administrator | Built-in: SYS<br/>Custom: Users granted the DBA role before enabling the separation of duties | ALTER DATABASE<br/>ALTER SYSTEM<br/>CREATE/ALTER SESSION<br/>CREATE/ALTER/DROP USER<br/>CREATE/ALTER/DROP (ANY) ROLE<br />CREATE/ALTER/DROP/UNLIMITED TABLESPACE<br/>CREATE TABLE<br/>CREATE TYPE <br />CREATE/ALTER/DROP DATABASE LINK<br />CREATE/ALTER/DROP PUBLIC DATABASE LINK<br />CREATE LIBRARY<br />CREATE SEQUENCE<br/>CREATE SYNONYM<br/>CREATE VIEW<br/>CREATE PROCEDURE<br/>CREATE TRIGGER<br/>CREATE/ALTER/DROP ANY OUTLINE<br/>CREATE/ALTER/DROP PROFILE<br/>CREATE/ALTER/DROP ANY MATERIALIZED VIEW<br/>CREATE/DROP ANY DIRECTORY<br/>CREATE/DROP ANY CONTEXT<br/>ANALYZE ANY<br />YSTREAM_CAPTURE<br />FILE<br />SELECT FROM SYS.OBJECT |
| Security Administrator | Built-in: SECURITOR<br/>Custom: Users granted the SECURITY_ADMIN role before enabling the separation of duties | CREATE SESSION<br/>GRANT ANY PRIVILEGE/OBJECT PRIVILEGE/ROLE<br/>SELECT ON SYS.USERAUTH$ (View permission system table) <br/>ALL PRIVILEGES ON SYS.ANON_POLICY$  (Dynamic data masking policy)<br/>ADMINISTER KEY MANAGEMENT<br/>LBAC_DBA role (Row-level access control related permissions)<br/>SELECT_CATALOG_ROLE role |
| Audit Administrator | Built-in: AUDITOR<br/>Custom: Users granted the AUDIT_ADMIN role before enabling the separation of duties | CREATE SESSION<br/>AUDIT SYSTEM<br/>SELECT_CATALOG_ROLE role |



## Label-Based Access Control

YashanDB provides Label-Based Access Control (LBAC), enabling strong row-level access control to precisely manage read/write permissions for users on table rows, ensuring data security.



LBAC is a form of strong access control that automatically adds a column to the target table to record the label for each row when a security policy is applied — enabling data classification and grading at the row level, and assigning different labels to users to restrict their access to data. The relationship between security policies and tables allows for many-to-many mapping — once a security policy is applied to a table, it takes effect immediately.

- Label Policy: Defines the format and validation rules for labels. By applying the policy, a relationship is established between the label and the table/user.

- Label: Composed of a level and a compartment, typically formatted as `level1,level2,…:compartment1,compartment2,…`.
    - Level: Used to define the sensitivity level of protected data — the higher the value, the higher the sensitivity (e.g., 10 for "Public", 20 for "Confidential").

    - Compartment: Used to classify protected data (e.g., 1 for "Employee Information", 2 for "Customer Information").

When a user attempts to access protected data, the system compares the user's security label with the label of the target data row:

- For read access: The user is only allowed to query rows that are authorized for reading, and must satisfy the following conditions:

    - The level value in the user's maximum read label (the level value corresponding to MAX_READ_LABEL in the DBA_SA_USER_LABELS view) is greater than or equal to the level value in the label of the target data row.

    - The compartment set in the user's label includes all the compartments in the target row's label.

- For write access (INSERT, UPDATE, DELETE): The user is only allowed to modify, delete, or insert rows that are authorized for writing, and must satisfy the following conditions:

    - The level value in the user's minimum write label (the level value corresponding to MIN_WRITE_LABEL in the DBA_SA_USER_LABELS view) is greater than or equal to the level value in the label of the target data row.
    
    - The compartment set in the user's label includes all the compartments in the target row's label.


