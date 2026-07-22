Before building a multi-tenant environment, corresponding preparatory work must be completed, such as checking to ensure the current environment meets requirements, tenant configuration planning, etc.

## Environment Requirements

To deploy YashanDB as a CDB, the following requirements must be met:

- Database version requirements

  Must use YashanDB v23.5 or higher version that supports multi-tenant features.

- Memory resources

  The system must reserve sufficient memory to support the simultaneous operation of the CDB root, PDB seed, and the expected number of PDBs.

- Disk storage space

  - Standalone (Primary-Standby) Deployment: Independent storage space must be reserved for the CDB root, PDB seed, and all PDBs.

  - YAC/Distributed Cluster Deployment: All data files and redo log files must be located in YFS, and sufficient storage space must be ensured.

## Key Parameter Configuration

Multitenant environments involve the following key and unique parameters that should be planned and adjusted according to requirements in advance.

| Parameter    | Description      | Value Range | Recommended Value |
| ------------------------- | ------------------------------ | ---- | -------- |
| ENABLE_PLUGGABLE_DATABASE | Identifies whether the current database is a CDB. | [TRUE&#124;FALSE], default is FALSE | Must be set to TRUE |
| CDB_MAX_MEMORY_SIZE       | Specify the upper limit of the total physical memory allowed to be used by the CDB root, PDB seed, and all PDBs.<br />The default value 0 indicates 80% of the server's total memory. | 0 or [2G,2<sup>64</sup>-1], default is 0 | Adjust as needed |
| MAX_PDBS                  | Specifies the maximum number of PDBs that can be created | * Standalone Deployment: [0,8190] <br/>* YAC/Distributed Cluster Deployment: [0,256]<br/>Default is 256 for both | Adjust as needed |
| COMMON_USER_PREFIX        | Specify the name prefix for global users, roles, and profiles. | Default is C## | Adjust as needed |

## Data Planning and Layout

- Physical data file storage path planning

    YashanDB defaults to creating separate folders for each container to save their respective physical files, with the default path being $YASDB_DATA/containers/{pdb_name}. Each container's file path can be adjusted as needed, and it is recommended to plan independent paths respectively to ensure data isolation.

- Business data ownership

    All user business data should be planned in PDBs, and independent PDBs can be planned for different business systems according to actual computing resources, storage space, and network access isolation requirements.

    It is recommended that the CDB root only be used to store global metadata, such as global users and global resource plans, and business data should not be stored.

- PDB quantity control

    The PDB total controlled by the MAX_PDBS parameter is only a theoretical upper limit. When actually planning PDB quantity, factors such as hardware capacity and business growth expectations should also be considered to prevent resource exhaustion.

- PDB syntax mode

    According to SQL syntax compatibility preferences, PDBs can be configured as yashan mode (more compatible with Oracle) or mysql mode (more compatible with MySQL) as needed. For detailed compatibility introductions, please refer to [Compatibility Description](../../../Product Overview/Compatibility/00Compatibility).