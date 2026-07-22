## General Description

CREATE PFILE is used to overwrite and update the parameter file (yasdb.ini). The parameter files of each instance/node are independent of each other.

The prerequisites for overwriting and updating the parameter file are as follows:

- The current instance/node has been started to the NOMOUNT stage.

- The user executing this statement must be the sys user or another user with SYSDBA, SYSOPER, or SYSBACKUP roles.

- The current instance/node already has a server parameter file (yasdb.spfile).

## Statement Definition

**create pfile::=**

```ebnf
= CREATE PFILE FROM SPFILE.
```

### FROM SPFILE

Overwrite and update the existing parameter file (yasdb.ini) based on the currently used server parameter file (yasdb.spfile).