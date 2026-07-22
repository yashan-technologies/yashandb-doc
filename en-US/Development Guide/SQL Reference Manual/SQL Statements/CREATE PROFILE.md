Generic Description
----

CREATE PROFILE is used to create a profile, which is a collection of resource limits imposed by the system on users. In YashanDB, profiles can define limitations related to [password usage policies](../../../Product Security/Identity Identification and Authentication/Password Authentication/Password Policy.md#password_policy), [session resources](../../../Product Security/Connection Management/Controlling Session Resources), and [connection request IPs](../../../Product Security/Connection Management/Managing IP Blacklist or Whitelist).

When executing this statement, the following points should be noted:

- The user must have the CREATE PROFILE privilege.
- The profile itself does not contain user information. The relationship between the user and the profile must be established using the [CREATE USER](CREATE USER) or [ALTER USER](ALTER USER) statements.

All profiles and their contents can be queried through the dba_profiles view.

YashanDB has a default profile (named DEFAULT) preconfigured. When creating a new user using the default syntax, that user will be associated with the default profile and will be subject to its specified configuration limits.

In ISC Distributed Cluster Deployment, only [resource limits related to password policies](#pswd) are supported.

> **Note**: 
>
> - Parameter values support implicit type conversions and expression operations, as long as the result satisfies the rules.
> - If all parameter values are empty strings or the expression result is NULL, they are treated as 0 value.

Statement Definition
----

**create profile::=**

```ebnf
= CREATE PROFILE profile_name LIMIT (password_parameters | resource_parameters | tcp_ip_parameters) {" " (password_parameters | resource_parameters | tcp_ip_parameters)} [CONTAINER "=" (CURRENT|ALL)].
```

**[password_parameters](#pswd)::=**

```ebnf
= password_parameter_name password_parameter_value
  {" " password_parameter_name password_parameter_value}.
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

The profile name is mandatory and must comply with YashanDB's [object naming specifications](../Basic SQL Elements/Identifiers).


In a CDB, global profile names must start with a prefix. The default prefix is C##, and the prefix value can be configured through the COMMON_USER_PREFIX parameter before creating global roles, global users, and global profiles. Local profile names can never start with C## or custom-specified prefixes.  


<span id="pswd" name="pswd"></span>

### password_parameters

Defines configurations related to [password usage policies](../../../Product Security/Identity Identification and Authentication/Password Authentication/Password Policy.md#password_policy), including password policy parameters and their values, with multiple parameters defined separated by spaces.

If a certain password policy parameter is not specified in this statement, the system will generate it in the profile according to the DEFAULT value (see the DEFAULT description below).

#### password_parameter_name

Names of the password usage policy parameters, with multiple parameters defined separated by spaces.

#### password_parameter_value

Specifies the values corresponding to the password policy parameters.

All parameters can be set to DEFAULT, UNLIMITED, or specific parameter values.

**DEFAULT**

Specifying DEFAULT means adopting the relevant configuration from the preset default profile.

**UNLIMITED**

Specifying UNLIMITED means there is no limitation on the parameter.

**password_parameters values**

Parameter values support mathematical operations, as long as the result meets the parameter value rules.

The relevant parameters are shown in the following table:

::: tabs
== When separation of duties is disabled

|Parameter |Corresponding Policy |Value Range | Default Value |
| --------------------- | ----------------------------|-------------------------|------- |
| FAILED_LOGIN_ATTEMPTS   | Maximum consecutive failed login attempts | An integer in the range (0, 2147483647) |  10  |
| FAILED_LOGIN_INTERVAL    | Password retry interval (unit: seconds), i.e., the waiting time required before retrying after a failed password attempt | UNLIMITED or an integer in the range [1,2147483646] |UNLIMITED<br>i.e., no limit on wait interval |
| PASSWORD_LOCK_TIME      | Number of days the user is locked after exceeding the incorrect attempts | (0,24855] | 1    |
| PASSWORD_LIFE_TIME      | Password lifecycle, i.e., the number of days before a password needs to be updated        | UNLIMITED or (0,24855]  | UNLIMITED, .e., no limit on days |
| PASSWORD_GRACE_TIME     | Password grace period, i.e., the number of days a password can still be used after expiration | UNLIMITED or [0,24855]  | UNLIMITED<br>i.e., no limit on grace period |
| PASSWORD_REUSE_TIME     | Number of days a password cannot be reused<br>If set to X, the new password cannot be one used in the last X days | UNLIMITED or (0,24855]      | UNLIMITED<br>i.e., no limit on reuse   |
| PASSWORD_REUSE_MAX      | Number of necessary changes before a password can be reused<br>If set to N, the new password cannot be one used in the last N changes |  UNLIMITED or an integer in the range [1,2147483646] | UNLIMITED<br>i.e., no limit on reuse   |

== When separation of duties is enabled

|Parameter |Corresponding Policy |Value Range | Default Value |
| --------------------- | ----------------------------|-------------------------|------- |
| FAILED_LOGIN_ATTEMPTS   | Maximum consecutive failed login attempts | An integer in the range (0,5] |  5  |
| FAILED_LOGIN_INTERVAL    | Password retry interval (unit: seconds), i.e., the waiting time required before retrying after a failed password attempt | UNLIMITED or an integer in the range [1,2147483646] |UNLIMITED<br>i.e., no limit on wait interval |
| PASSWORD_LOCK_TIME      | Number of days the user is locked after exceeding the incorrect attempts | (0,24855] | 1    |
| PASSWORD_LIFE_TIME      | Password lifecycle, i.e., the number of days before a password needs to be updated        | (0,7] | 7 |
| PASSWORD_GRACE_TIME     | Password grace period, i.e., the number of days a password can still be used after expiration | 0  | 0 |
| PASSWORD_REUSE_TIME     | Number of days a password cannot be reused<br>If set to X, the new password cannot be one used in the last X days | UNLIMITED or (0,24855]      | UNLIMITED<br>i.e., no limit on reuse   |
| PASSWORD_REUSE_MAX      | Number of necessary changes before a password can be reused<br>If set to N, the new password cannot be one used in the last N changes |  UNLIMITED or an integer in the range [1,2147483646] | UNLIMITED<br>i.e., no limit on reuse   |

> **Note**:
>
> After enabling separation of duties,  the custom profiles created previously are not affected. However, some values of the default profile will be automatically adjusted to comply with separation of duties requirements. When separation of duties is later disabled, these PROFILE values will not be reset to their original state.

:::

- FAILED_LOGIN_ATTEMPTS, FAILED_LOGIN_INTERVAL, and PASSWORD_LOCK_TIME work together to achieve the effect of "waiting X seconds before retrying after a password error, locking the user for Z days after Y consecutive retries (unless the user is manually [unlocked](../../../Product Security/Identity Identification and Authentication/Managing Users.md#alteruser))."

- PASSWORD_REUSE_TIME and PASSWORD_REUSE_MAX work together to achieve the effect of "the new password cannot be the same as a password used in the last N days and the last M times." If one parameter is set to a specific value while the other is set to UNLIMITED, the user can never use a repeated password.

***Example***

```sql
CREATE PROFILE prof_pswd1 LIMIT FAILED_LOGIN_ATTEMPTS 5 PASSWORD_LIFE_TIME 180;

CREATE PROFILE prof_pswd2 LIMIT 
FAILED_LOGIN_ATTEMPTS 15 
FAILED_LOGIN_INTERVAL 10
PASSWORD_LOCK_TIME 1 
PASSWORD_LIFE_TIME 30*2.5 
PASSWORD_GRACE_TIME 0
PASSWORD_REUSE_TIME UNLIMITED 
PASSWORD_REUSE_MAX UNLIMITED;
```

<span id="res" name="res"></span>

### resource_parameters

Defines configurations related to [session resources](../../../Product Security/Connection Management/Controlling Session Resources), with multiple parameters defined separated by spaces.

If a certain resource policy parameter is not specified in this statement, the system will generate it in the profile according to the DEFAULT value (see the DEFAULT description below).

#### resource_parameter_name

Currently includes SESSIONS_PER_USER and IDLE_TIME.

**SESSIONS_PER_USER**

The maximum number of concurrent session connections for the same user.

**IDLE_TIME**

The maximum idle time without user operation, in minutes. If a user is logged in but remains inactive for a prolonged time that exceeds the threshold, the system will disconnect the session. If an operation is attempted after disconnection, an error will occur.

The background thread checks every 10 seconds and disconnects long idle sessions, so the maximum continuous idle time for users may be up to 10 seconds more than the corresponding threshold.

#### resource_parameter_value

Specifies the values corresponding to the session resource parameters.

All parameters can be set to DEFAULT, UNLIMITED, or specific parameter values.

**DEFAULT**

Specifying DEFAULT means adopting the relevant configuration from the preset default profile.

**UNLIMITED**

Specifying UNLIMITED means there is no limitation on the corresponding resource parameter.

**resource_parameters values**

Parameter values support expression operations, as long as the results satisfy the parameter value rules.

|Policy Parameter |Parameter Description |Parameter Value Rules |
| --------------------- | ------------------------- | ----------------------------------- |
| SESSIONS_PER_USER         | The maximum number of concurrent session connections for the same user | An integer in the range of [1,2147483646]                                |
| IDLE_TIME                 | The maximum idle time without user operation (unit: minutes) | An integer in the range of [1,2147483646]              |

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
CREATE PROFILE prof_res LIMIT SESSIONS_PER_USER 10 IDLE_TIME 10;
```

<span id="ip" name="ip"></span>

### tcp_ip_parameters

Defines configurations related to [connection request IPs](../../../Product Security/Connection Management/Managing IP Blacklist or Whitelist), with multiple parameters defined separated by spaces.

If a certain connection request IP configuration parameter is not specified in this statement, the system will generate it in the profile according to the DEFAULT value (see the DEFAULT description below).

#### tcp_ip_parameters_name

Currently includes login IP whitelist INVITED_NODES and login IP blacklist EXCLUDED_NODES.

The blacklist has higher priority than the whitelist; if an IP exists in both, the blacklist takes effect.

**INVITED_NODES**

Login IP whitelist. Once the IP list is configured, only users associated with the current profile can log in using the IPs in the whitelist.

The number of unique IPs must not exceed 64.

**EXCLUDED_NODES**

Login IP blacklist. Once the IP list is configured, users associated with the current profile cannot log in using the IPs in the blacklist.

The number of unique IPs must not exceed 64.

#### tcp_ip_parameters_value

Specifies the values corresponding to the connection request IP parameters.

All parameters can be set to DEFAULT, UNLIMITED, or specific IP lists.

**DEFAULT**

Specifying DEFAULT means no restrictions, which is equivalent to UNLIMITED.

**UNLIMITED**

Specifying UNLIMITED means there is no limitation on the corresponding parameter.

**iplist**

IP list string, formatted as `'IP/mask,IP'`.

- IP: actual IP address, e.g., 192.168.4.11.
- MASK: indicates the number of bits to match the binary IP from left to right, in the range of [0,32]. If MASK is omitted, it defaults to full match.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
CREATE PROFILE prof_ip LIMIT EXCLUDED_NODES '192.168.4.11';
```

### CONTAINER



This statement is only applicable to a CDB (with configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), used to specify the container scope for the current operation, which can be set to take effect globally or locally within the current container. It can be omitted.

CONTAINER attribute optional values are as follows:

- CURRENT: Indicates effectiveness only in the currently connected container locally. When directly connecting to PDB, only this value can be specified or completely omitted, with the same effect.

- ALL: Indicates global effectiveness. Only available when connecting to the CDB root and defaults to this value when omitted.




***Example*** for Standalone/YAC/Distributed Cluster Deployment  

```sql
CREATE PROFILE c##prof_all LIMIT SESSIONS_PER_USER 10 IDLE_TIME 10 CONTAINER = all;
```
