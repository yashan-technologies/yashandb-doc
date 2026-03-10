## User

A user, which is an account that logs into the database, is a logical object that can access the database. Users in YashanDB are divided into:

- System User

    A system user is a user preset in YashanDB, a super administrator responsible for managing the database system and has all privileges. The account name is sys, and the password must be generated and maintained using the *yaspwd* tool.

- Regular User

    A user created using SQL statements is a regular user. The identity of the regular user can be clarified by granting them privileges, such as DBA, Security Administrator, Audit Administrator, etc.

## Privilege

YashanDB restricts users to operate the database only within the limits of their privileges to ensure database security.

### System Privileges

System privileges are a category of privileges that support executing certain system-level operations. They may apply to objects or database-level operations.

You can view all authorized system privilege information in the current system through the system privilege view.

The functionality of system privileges is quite extensive, so please grant them cautiously. Usually, only administrators perform the granting of system privileges to ensure that the authorized user or role is trustworthy.

System privileges include:

- System Management Privileges: Manage operations on the database, tablespaces, and session objects.

- Security Management Privileges: Manage users, roles, and authorization operations.

- Object Operation Privileges: Manage objects such as tables, indexes, views, sequences, synonyms, UDTs, stored procedures, triggers, custom libraries, database links, and materialized views.

- Other System Privileges: Manage storage schemas, statistics, and PROFILES, etc.

### Object Privileges

Object privileges allow users to perform specified operations on designated objects. YashanDB supports object privileges based on various object operation privileges related to tables, including ALL PRIVILEGES, INSERT, SELECT, UPDATE, DELETE, ALTER, INDEX, FLASHBACK, and READ.

## Role

A role is a collection of privileges and other roles. Roles help manage the privileges of users or applications, allowing users to grant roles to other users or roles.

When a set of privileges is assigned to a role, the role can participate in the privilege system. Using roles can significantly simplify privilege management operations. For example, after granting a created role to a user, the user possesses all privileges that the role currently and in the future has. System administrators only need to focus on users' roles, and for roles, only on their privilege sets, simplifying privilege management.

Roles in YashanDB are divided into:

- Built-in (System Predefined) Roles

    Built-in roles in YashanDB include: DBA, SYSDBA, SYSOPER, AUDIT_ADMIN, SECURITY_ADMIN, PUBLIC, CONNECT, RESOURCE, and SELECT_CATALOG_ROLE.

- Custom Roles

    Users can design corresponding roles based on their privilege mechanisms, including the following information:

    - Role Name: A meaningful unique identifier associated with the set of privileges included in the role, which cannot have the same name as any existing users, roles, or privileges in the system.
    
    - Included Privileges: Granting a privilege to a role incorporates that privilege into the set of privileges contained within the role.

## Authorization and Revocation

YashanDB provides functionality for authorization or revocation, allowing customers to manage privileges according to their actual business needs, enabling database administrators to manage database access behaviors more efficiently and ensure database security.

- Authorization: After confirming that the user (or role) to be authorized is safe and trustworthy, and their required privileges and roles (or privileges) match, roles (or privileges) can be granted to the user.

- Revocation: After confirming that the user (or role) being revoked no longer needs that role (or privilege), and that the revocation will not affect normal business operations, roles (or privileges) can be revoked from the user.

## Profile

User resource profiles are a set of resource limitations assigned to users by YashanDB, which can be shared among multiple users. In YashanDB, profiles are mainly used to define limits related to password policies.