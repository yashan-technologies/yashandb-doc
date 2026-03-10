This article will introduce the OCI connection configuration methods for YashanDB.

## Using URL Connection

Directly use the URL connection, for example:

- Use IPv4 connection: `192.168.1.2:1688`
    
- Use HA scenario multi-IP primary mode (the `PRIMARY:` keyword can be omitted): `PRIMARY:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688`

- Use HA scenario multi-IP standby mode: `STANDBY:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688`
    
- Use multi-IP loadBalance mode: `LOADBALANCE:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688`.

- Use multi-IP primaryLoadBalance mode: `PRIMARYLOADBALANCE:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688`.

- Use multi-IP standbyLoadBalance mode: `STANDBYLOADBALANCE:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688`.

## Using Alias Connection

To configure the connection using an alias in the URL, you must first define DBNAME in `$YASDB_HOME/client/yasc_service.ini`, for example:

```shell
vi yasc_service.ini

CITEST = 192.168.1.2:1688
REMOTE = PRIMARY:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688
REMOTE2 = STANDBY:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688
ABROAD = LOADBALANCE:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688
ABROAD2 = PRIMARYLOADBALANCE:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688
ABROAD3 = STANDBYLOADBALANCE:192.168.1.2:1688,192.168.1.3:1688,192.168.1.4:1688
```
