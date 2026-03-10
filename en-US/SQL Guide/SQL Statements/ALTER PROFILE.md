General Description
----

ALTER PROFILE is used to modify the contents of a profile.

When executing this statement, the following points must be noted:

- The user must have the ALTER PROFILE privilege to modify a profile.
- For profiles already associated with a user, changes made to the profile will take effect immediately on the user's resource limits.

The default profile for YashanDB (named DEFAULT) can also be modified using this statement.

Statement Definition
----

**alter profile::=**

```ebnf+diagram
syntax::= ALTER PROFILE profile_name LIMIT  (password_parameters | resource_parameters | tcp_ip_parameters) {" " (password_parameters | resource_parameters | tcp_ip_parameters)}
```

**[password_parameters](#pswd)::=**

```ebnf+diagram
syntax::= (password_parameter_name password_parameter_value)
  {" " (password_parameter_name password_parameter_value)}
```

**[resource_parameters](#res)::=**

```ebnf+diagram
syntax::= (resource_parameter_name resource_parameter_value)
  {" " (resource_parameter_name resource_parameter_value)}
```

**[tcp_ip_parameters](#ip)::=**

```ebnf+diagram
syntax::= (tcp_ip_parameter_name tcp_ip_parameter_value)
  {" " (tcp_ip_parameter_name tcp_ip_parameter_value)}
```

### 1. profile\_name

The name of an existing profile.

<span id="pswd" name="pswd" class="yaslink"></span>

### 2. password\_parameters

Defines configurations related to password policies, and modifies multiple parameters separated by spaces.

The syntax and rules are the same as in [password_parameters](CREATE PROFILE.html#pswd) of CREATE PROFILE.

<span id="res" name="res" class="yaslink"></span>

### 3. resource\_parameters

Defines configurations related to session resources, and modifies multiple parameters separated by spaces.

The syntax and rules are the same as in [resource_parameters](CREATE PROFILE.html#res) of CREATE PROFILE.

<span id="ip" name="ip" class="yaslink"></span>

### 4. tcp\_ip\_parameters

Defines configurations related to connection request IPs, and modifies multiple parameters separated by spaces.

The syntax and rules are the same as in [tcp_ip_parameters](CREATE PROFILE.html#ip) of CREATE PROFILE.

***Example***

```sql
ALTER PROFILE prof_pswd1 LIMIT FAILED_LOGIN_ATTEMPTS 3 PASSWORD_LIFE_TIME 200;
```

***Example*** for Standalone Deployment and YAC Deployment

```sql
ALTER PROFILE prof_res LIMIT SESSIONS_PER_USER 10 IDLE_TIME UNLIMITED;

ALTER PROFILE prof_ip LIMIT EXCLUDED_NODES '192.168.4.11,192.168.4.12';
```
