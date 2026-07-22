General Description
----

ALTER PROFILE is used to modify the contents of a profile.

When executing this statement, the following points must be noted:

- The user must have the ALTER PROFILE privilege to modify a profile.
- For profiles already associated with a user, changes made to the profile will take effect immediately on the user's resource limits.

The default profile for YashanDB (named DEFAULT) can also be modified using this statement.

In a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), modifying a global profile must be done by connecting to the CDB root.

Statement Definition
----

**alter profile::=**

```ebnf
= ALTER PROFILE profile_name LIMIT  (password_parameters | resource_parameters | tcp_ip_parameters) {" " (password_parameters | resource_parameters | tcp_ip_parameters)} [CONTAINER "=" (CURRENT|ALL)].
```

**[password_parameters](#pswd)::=**

```ebnf
= (password_parameter_name password_parameter_value)
  {" " (password_parameter_name password_parameter_value)}.
```

**[resource_parameters](#res)::=**

```ebnf
= resource_parameter_name resource_parameter_value
  {" " resource_parameter_name resource_parameter_value}.
```

**[tcp_ip_parameters](#ip)::=**

```ebnf
= tcp_ip_parameter_name tcp_ip_parameter_value
  {" " tcp_ip_parameter_name tcp_ip_parameter_value}.
```

### profile_name

The name of an existing profile.

<span id="pswd" name="pswd"></span>

### password_parameters

Defines configurations related to password policies, and modifies multiple parameters separated by spaces.

The syntax and rules are the same as in [password_parameters](CREATE PROFILE.md#pswd) of CREATE PROFILE.

<span id="res" name="res"></span>

### resource_parameters

Defines configurations related to session resources, and modifies multiple parameters separated by spaces.

The syntax and rules are the same as in [resource_parameters](CREATE PROFILE.md#res) of CREATE PROFILE.

<span id="ip" name="ip"></span>

### tcp_ip_parameters

Defines configurations related to connection request IPs, and modifies multiple parameters separated by spaces.

The syntax and rules are the same as in [tcp_ip_parameters](CREATE PROFILE.md#ip) of CREATE PROFILE.

### CONTAINER



This statement is only applicable to CDBs (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), used to specify the container scope for the current operation, which can be set to take effect globally or locally within the current container. When omitted, it defaults to local effectiveness.

CONTAINER attribute optional values are as follows:

- CURRENT: Indicates local effectiveness only in the currently connected container, which can only be specified when directly connecting to a PDB.

- ALL: Indicates global effectiveness, which is only available when connecting to the CDB root.



***Example***

```sql
ALTER PROFILE prof_pswd1 LIMIT FAILED_LOGIN_ATTEMPTS 3 PASSWORD_LIFE_TIME 200;
```

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
ALTER PROFILE prof_res LIMIT SESSIONS_PER_USER 10 IDLE_TIME UNLIMITED;

ALTER PROFILE prof_ip LIMIT EXCLUDED_NODES '192.168.4.11,192.168.4.12';

ALTER PROFILE c##prof_all LIMIT SESSIONS_PER_USER 15 IDLE_TIME 10 CONTAINER = all;
```
