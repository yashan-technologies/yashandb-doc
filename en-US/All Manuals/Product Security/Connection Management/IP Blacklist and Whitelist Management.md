YashanDB provides an IP access control functionality for managing remote connection requests, enhancing database access security (requests using UDS local connections are not affected).

YashanDB supports configuring an IP whitelist to allow only specified IPs to initiate remote connection requests, and it also supports configuring an IP blacklist to disallow only specified IPs from initiating remote connection requests. The blacklist takes precedence over the whitelist; if the same IP exists in both lists, the rules in the blacklist will apply.

Additionally, based on the profile, IP black and white lists can be configured to be effective only for specific users.

>  **Note**: 
>
> This functionality is effective only for remote connections; requests using UDS local connections will always be allowed.

## Global IP Black and White List Management

The global IP black and white list management applies to all remote connection requests made to the database. Changes to the configuration require a database restart to take effect.

### Enable IP Black and White List Management Functionality

The switch for this functionality is controlled by the TCP.VALIDNODE_CHECKING parameter in the database configuration file yasdb_net.ini, which is off by default.

After YashanDB installation, the yasdb_net.ini configuration file will not be automatically generated. If modifications are needed, you should manually create this file in the $YASDB_DATA/config path before making changes. Configuring the TCP.VALIDNODE_CHECKING parameter to yes enables this functionality (the system is case insensitive), while other values will disable it.

After enabling this functionality, you must also configure IP whitelist or blacklist.

```ini
## Path is $YASDB_DATA/config/yasdb_net.ini
TCP.VALIDNODE_CHECKING = YES
TCP.INVITED_NODES = 192.168.1.2/16
TCP.EXCLUDED_NODES = 192.168.1.111/32
```

### Configure IP Black/White List

> **Warn**:
>
> IPs in the black/white list must be configured in a format recognized as valid by the system; invalid IPs will cause the database instance to fail to start.

#### IP Whitelist TCP.INVITED\_NODES

The IP whitelist is configured using the TCP.INVITED_NODES parameter, with multiple IPs separated by a comma.

The format of the IP is: IP[/MASK], where MASK indicates the number of bits from left to right that match the binary IP. The MASK for IPv4 addresses ranges from [0,32], defaulting to 32 for full match; the MASK for IPv6 addresses ranges from [0,128], defaulting to 128 for full match. For example, 127.0.0.1/8 matches only the first byte of the IP 127, while fc00:7::126/64 matches the first 8 bytes of the IP.

- Wildcards are not allowed to represent IPs, e.g., configuring 127.*.*.0 will be considered an invalid IP.

- Domain names are not allowed to replace IPs.

- 127.0.0.1 cannot be used to refer to the current server (localhost) IP, the actual IP address must be used.

#### IP Blacklist TCP.EXCLUDED\_NODES

The IP blacklist is configured using the TCP.EXCLUDED_NODES parameter, with multiple IPs separated by a comma, and the format is the same as the whitelist.

## Specific IP Black and White List Management

The specific IP black and white list management is effective only for remote connection requests initiated by users associated with the profile. After creating the corresponding profile, it must be linked to a user to take effect. Changes to an existing profile will take effect immediately for its associated users.

- Executing the [CREATE PROFILE](../../Development Guide/SQL Reference Manual/SQL Statements/CREATE PROFILE.html#ip) and [ALTER PROFILE](../../Development Guide/SQL Reference Manual/SQL Statements/ALTER PROFILE.html#ip) statements can configure the IP black/white list.

- Executing the [CREATE USER](../../Development Guide/SQL Reference Manual/SQL Statements/CREATE USER.html#profile) or [ALTER USER](../../Development Guide/SQL Reference Manual/SQL Statements/ALTER USER.html#profile) statements can associate users with profiles, i.e., configured users for the IP black/white list to take effect.

### IP Whitelist INVITED\_NODES

The IP whitelist is configured using the INVITED_NODES parameter, with a format of `'IP/mask,IP'`, where multiple IPs are separated by commas, and the number of unique IPs must not exceed 64.

- IP: Actual IP address, for example, 192.168.4.11.

- MASK: Indicates the number of bits from left to right that match the binary IP, with a range of [0,32]. If no MASK is specified, it defaults to a full match.

### IP Blacklist EXCLUDED\_NODES

The IP blacklist is configured using the EXCLUDED_NODES parameter, with the format and constraints being the same as the whitelist.