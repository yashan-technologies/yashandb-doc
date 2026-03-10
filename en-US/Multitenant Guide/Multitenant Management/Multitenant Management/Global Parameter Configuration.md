## Configuration Parameter Classification



In a CDB, configuration parameters can be classified according to their effectiveness attributes:

- CDB global parameters: CDB-level configurations, only allowed to be modified when connecting to the CDB root, and only take effect on the CDB root.

- CDB private parameters: Independent parameters for each container (the CDB root, PDB), can only be customized by directly connecting to the target container, and only take effect on themselves.

- Common parameters: Each container can customize the configuration or directly inherit parameters from the CDB root (the old value of the corresponding parameter in PDB being consistent with the old value of the CDB root indicates an inheritance relationship). The scope of effectiveness depends on the executing instance and the specified CONTAINER attribute.


The scope attributes of parameters can be determined through the ISPDB_MODIFIABLE and ISPDB_PRIVATE fields in the [V$PARAMETER](../../../All Manuals/Reference Manual/System Views/Dynamic Performance Views/V$PARAMETER) view.

## Configuration Inheritance and Compensation Mechanism

PDB's common parameter class configurations default to inheriting from the CDB root. If a common parameter value in PDB matches the value in the CDB root, it is considered to have an inheritance relationship. Whether an inheritance relationship exists will directly impact the operational approach for bulk distribution of PDB configuration adjustments:

- With inheritance relationship: When modifying configuration parameters with CONTAINER=current specified in the CDB root, PDBs will still inherit such changes.

- Inheritance relationship removed: Modifications must be made with CONTAINER=all specified in the CDB root, and the inheritance relationship is restored after modification completion.

The configuration compensation mechanism mainly refers to how to correctly apply the latest common parameter class configurations when PDB starts:

- When creating a new PDB, directly inherit and apply the latest effective corresponding configurations from the CDB root.

- When starting an existing PDB, compensate for applying configuration adjustments bulk distributed by the CDB root during PDB shutdown.

## Parameter Value Modification Methods

In a CDB, based on actual operations requirements, configuration parameters can be centrally managed at CDB level or independently managed by each PDB.

### Modifying Using SQL Statements

#### Global Batch Modification

CDB administrators can log into the CDB root to perform global batch modifications on common parameter class configurations, distributing change instructions to all PDBs or PDBs with inheritance relationships.

1. Connect and log in to the CDB root as a DBA user.
    ```shell
    $ yasql c##sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. Execute ALTER SYSTEM statement to batch modify the values of common parameters.

    - Only adjust common parameters of **PDBs with inheritance relationships**

    ```sql
    SELECT a.NAME AS CON_NAME, c.NAME AS parameter, c.VALUE
    FROM containers(V$PARAMETER) AS c
    INNER JOIN V$CONTAINERS a ON c.CON_ID = a.CON_ID
    WHERE c.NAME = 'CHECKPOINT_TIMEOUT';
    CON_NAME     PARAMETER                                             VALUE
    ------------ ----------------------------------------------------- ----------------------
    CDB$ROOT     CHECKPOINT_TIMEOUT                                    300
    PDB1         CHECKPOINT_TIMEOUT                                    1000
    PDB2         CHECKPOINT_TIMEOUT                                    300  

    ALTER SYSTEM SET CHECKPOINT_TIMEOUT=400 CONTAINER=CURRENT;

    SELECT a.NAME AS CON_NAME, c.NAME AS parameter, c.VALUE
    FROM containers(V$PARAMETER) AS c
    INNER JOIN V$CONTAINERS a ON c.CON_ID = a.CON_ID
    WHERE c.NAME = 'CHECKPOINT_TIMEOUT';
    CON_NAME NAME                                                      VALUE
    ------------ ----------------------------------------------------- ----------------------
    CDB$ROOT     CHECKPOINT_TIMEOUT                                    400
    PDB1         CHECKPOINT_TIMEOUT                                    1000
    PDB2         CHECKPOINT_TIMEOUT                                    400
    ```

    - Adjust common parameters of **all PDBs** (regardless of inheritance relationships)

    ```sql
    SELECT a.NAME AS CON_NAME, c.NAME AS parameter, c.VALUE
    FROM containers(V$PARAMETER) AS c
    INNER JOIN V$CONTAINERS a ON c.CON_ID = a.CON_ID
    WHERE c.NAME = 'CHECKPOINT_TIMEOUT';
    CON_NAME     PARAMETER                                             VALUE
    ------------ ----------------------------------------------------- ----------------------
    CDB$ROOT     CHECKPOINT_TIMEOUT                                    400
    PDB1         CHECKPOINT_TIMEOUT                                    1000
    PDB2         CHECKPOINT_TIMEOUT                                    400

    ALTER SYSTEM SET CHECKPOINT_TIMEOUT=300 CONTAINER=ALL;

    SELECT a.NAME AS CON_NAME, c.NAME AS parameter, c.VALUE
    FROM containers(V$PARAMETER) AS c
    INNER JOIN V$CONTAINERS a ON c.CON_ID = a.CON_ID
    WHERE c.NAME = 'CHECKPOINT_TIMEOUT';
    CON_NAME     PARAMETER                                             VALUE
    ------------ ----------------------------------------------------- ----------------------
    CDB$ROOT     CHECKPOINT_TIMEOUT                                    300
    PDB1         CHECKPOINT_TIMEOUT                                    300
    PDB2         CHECKPOINT_TIMEOUT                                    300
    ```

#### Targeted Modification

CDB administrators can log into the CDB root to perform targeted modifications on common parameter class configurations for specified PDBs, with effects equivalent to logging into the PDB for local modifications.

1. Connect and log in to the CDB root as a DBA user.
    ```shell
    $ yasql c##sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. Execute ALTER SYSTEM statement to targeted adjust common parameters in specified PDBs.
    
    ```sql
    SELECT a.NAME AS CON_NAME, c.NAME AS parameter, c.VALUE
    FROM containers(V$PARAMETER) AS c
    INNER JOIN V$CONTAINERS a ON c.CON_ID = a.CON_ID
    WHERE c.NAME = 'CHECKPOINT_TIMEOUT';
    CON_NAME     PARAMETER                                             VALUE
    ------------ ----------------------------------------------------- ----------------------
    CDB$ROOT     CHECKPOINT_TIMEOUT                                    300
    PDB1         CHECKPOINT_TIMEOUT                                    300
    PDB2         CHECKPOINT_TIMEOUT                                    300

    ALTER SYSTEM SET CHECKPOINT_TIMEOUT=350 CONTAINER=PDB1;

    SELECT a.NAME AS CON_NAME, c.NAME AS parameter, c.VALUE
    FROM containers(V$PARAMETER) AS c
    INNER JOIN V$CONTAINERS a ON c.CON_ID = a.CON_ID
    WHERE c.NAME = 'CHECKPOINT_TIMEOUT';
    CON_NAME     PARAMETER                                             VALUE
    ------------ ----------------------------------------------------- ----------------------
    CDB$ROOT     CHECKPOINT_TIMEOUT                                    300
    PDB1         CHECKPOINT_TIMEOUT                                    350
    PDB2         CHECKPOINT_TIMEOUT                                    300
    ```

#### Local Modification

Administrators can log into the target container (the CDB root or PDB) to perform local modifications on all parameters of that container.

1. Connect and log in to the target container (the CDB root or PDB as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. Execute ALTER SYSTEM statement to modify parameter values local to the current container.
    
    - When connecting to the CDB root, CONTAINER=CURRENT can be specified to modify CDB private parameters of the current container.

    ```sql
    SELECT a.NAME AS CON_NAME, c.NAME AS parameter, c.VALUE
    FROM containers(V$PARAMETER) AS c
    INNER JOIN V$CONTAINERS a ON c.CON_ID = a.CON_ID
    WHERE c.NAME = 'HA_ELECTION_AUTO_PRIMARY_SWITCH';
    CON_NAME     PARAMETER                                             VALUE
    ------------ ----------------------------------------------------- ----------------------
    CDB$ROOT     HA_ELECTION_AUTO_PRIMARY_SWITCH                       FALSE
    PDB1         HA_ELECTION_AUTO_PRIMARY_SWITCH                       FALSE
    PDB2         HA_ELECTION_AUTO_PRIMARY_SWITCH                       FALSE

    ALTER SYSTEM SET HA_ELECTION_AUTO_PRIMARY_SWITCH='TRUE' CONTAINER=CURRENT;

    SELECT a.NAME AS CON_NAME, c.NAME AS parameter, c.VALUE
    FROM containers(V$PARAMETER) AS c
    INNER JOIN V$CONTAINERS a ON c.CON_ID = a.CON_ID
    WHERE c.NAME = 'HA_ELECTION_AUTO_PRIMARY_SWITCH';
    CON_NAME     PARAMETER                                             VALUE
    ------------ ----------------------------------------------------- ----------------------
    CDB$ROOT     HA_ELECTION_AUTO_PRIMARY_SWITCH                       TRUE
    PDB1         HA_ELECTION_AUTO_PRIMARY_SWITCH                       FALSE
    PDB2         HA_ELECTION_AUTO_PRIMARY_SWITCH                       FALSE
    ```

    - When connecting to PDB, common parameters and CDB private parameters of the current container can be modified.

    ```shell
    $ yasql sales/********@192.168.1.2:1688/pdb1

    SQL> SHOW PARAMETER UNDO_RETENTION;
    NAME                        VALUE   
    --------------------------- ------- 
    UNDO_RETENTION              300  

    SQL> ALTER SYSTEM SET UNDO_RETENTION=350;

    SQL> SHOW PARAMETER UNDO_RETENTION;
    NAME                        VALUE   
    --------------------------- ------- 
    UNDO_RETENTION              350 

    SQL> exit

    $ yasql sys/********@192.168.1.2:1688

    SQL> SELECT a.NAME AS CON_NAME, c.NAME AS parameter, c.VALUE
    FROM containers(V$PARAMETER) AS c
    INNER JOIN V$CONTAINERS a ON c.CON_ID = a.CON_ID
    WHERE c.NAME = 'UNDO_RETENTION';
    CON_NAME     PARAMETER                                             VALUE
    ------------ ----------------------------------------------------- ----------------------
    CDB$ROOT     UNDO_RETENTION                                        300
    PDB1         UNDO_RETENTION                                        350
    PDB2         UNDO_RETENTION                                        300
    ```

### Modifying Using *yasboot*

The [yasboot cluster config set](../../../All Manuals/Tools Guide/yasboot/Introduction to yasboot Command/yasboot cluster) command is equivalent to executing ALTER SYSTEM SET … CONTAINER=CURRENT statement after logging into the CDB root. Use cases include:

- Batch adjusting common parameters in the CDB root and PDBs with inheritance relationships.

- Adjusting CDB private parameters in the CDB root.

Specific operational steps are as follows:

1. Log in to the database installation server using the installation user.


2. Execute the following command to view the current configuration of the target parameter.

    ```shell
    $ yasql / as sysdba

    SQL> SELECT a.NAME AS CON_NAME, c.NAME AS parameter, c.VALUE
    FROM containers(V$PARAMETER) AS c
    INNER JOIN V$CONTAINERS a ON c.CON_ID = a.CON_ID
    WHERE c.NAME = 'CHECKPOINT_TIMEOUT';
    CON_NAME     PARAMETER                                             VALUE
    ------------ ----------------------------------------------------- ----------------------
    CDB$ROOT     CHECKPOINT_TIMEOUT                                    300
    PDB1         CHECKPOINT_TIMEOUT                                    350
    PDB2         CHECKPOINT_TIMEOUT                                    300

    SQL> exit
    ```

3. Execute the following command to modify the parameter configuration.

    ```shell
    $ yasboot cluster config set -c yashandb -k CHECKPOINT_TIMEOUT -v 3600
    ```

4. Execute the following command to check the modification results.

    ```shell
    $ yasql / as sysdba

    SQL> SELECT a.NAME AS CON_NAME, c.NAME AS parameter, c.VALUE
    FROM containers(V$PARAMETER) AS c
    INNER JOIN V$CONTAINERS a ON c.CON_ID = a.CON_ID
    WHERE c.NAME = 'CHECKPOINT_TIMEOUT';
    CON_NAME     PARAMETER                                             VALUE
    ------------ ----------------------------------------------------- ----------------------
    CDB$ROOT     CHECKPOINT_TIMEOUT                                    3600
    PDB1         CHECKPOINT_TIMEOUT                                    350
    PDB2         CHECKPOINT_TIMEOUT                                    3600
    ```
