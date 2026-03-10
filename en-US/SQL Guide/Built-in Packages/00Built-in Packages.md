YashanDB provides a range of built-in advanced packages, which encapsulate various subprograms related to system logic for calling, classified by program purpose as follows:

- System standard functionality and transaction processing related

    [DBMS_CRYPTO](DBMS_CRYPTO)

    [DBMS_DESCRIBE](DBMS_DESCRIBE)

    [DBMS_LOB](DBMS_LOB)
	
	[DBMS_XMLDOM](DBMS_XMLDOM)
	
	[DBMS_XMLPARSER](DBMS_XMLPARSER)

    [DBMS_LOCK](DBMS_LOCK)

    [DBMS_METADATA](DBMS_METADATA)

    [DBMS_MVIEW](DBMS_MVIEW)

    [DBMS_OUTPUT](DBMS_OUTPUT)

    [DBMS_RANDOM](DBMS_RANDOM)

    [DBMS_ROWID](DBMS_ROWID)

    [DBMS_STANDARD](DBMS_STANDARD)

    [DBMS_SQL](DBMS_SQL)

    [DBMS_XA](DBMS_XA)

    [UTL_FILE](UTL_FILE)

    [UTL_ENCODE](UTL_ENCODE)
	
	[UTL_HTTP](UTL_HTTP)
	
	[UTL_TCP](UTL_TCP)

    [DBMS_DATAMASK](DBMS_DATAMASK)

    [ODCICONST](ODCICONST)

    [DBMS_ASSERT](DBMS_ASSERT)

- Scheduled tasks related

    [DBMS_IJOB](DBMS_IJOB)

    [DBMS_JOB](DBMS_JOB)

    [DBMS_SCHEDULER](DBMS_SCHEDULER)
    
    [DBMS_TASK](DBMS_TASK)

- Fault diagnosis related

    [DBMS_HM](DBMS_HM)

- Performance optimization related

    [DBMS_AWR](DBMS_AWR)

    [DBMS_AWR_EXTRA](DBMS_AWR_EXTRA)

    [DBMS_STATS](DBMS_STATS)

    [DBMS_PARAM](DBMS_PARAM)

- Resource management related

    [DBMS_RESOURCE_MANAGER](DBMS_RESOURCE_MANAGER)

- Audit related

    [DBMS_AUDIT_MGMT](DBMS_AUDIT_MGMT)

- Utility related

    [DBMS_UTILITY](DBMS_UTILITY)

    [OWA_UTIL](OWA_UTIL)

    [DBMS_PICKLER](DBMS_PICKLER)
    
    [UTL_RAW](UTL_RAW)

    [UTL_I18N](UTL_I18N)

    [DBMS_SESSION](DBMS_SESSION)

    [DBMS_APPLICATION_INFO](DBMS_APPLICATION_INFO)

- Network performance related

    [DBMS_DIN](DBMS_DIN)

- Log parsing related

    [DBMS_YSTREAM_ADM](DBMS_YSTREAM_ADM)

- Row access control related

    [YLS_ENFORCEMENT](YLS_ENFORCEMENT)

    [SA_SYSDBA](SA_SYSDBA)

    [SA_COMPONENTS](SA_COMPONENTS)

    [SA_LABEL_ADMIN](SA_LABEL_ADMIN)

    [SA_POLICY_ADMIN](SA_POLICY_ADMIN)

    [SA_USER_ADMIN](SA_USER_ADMIN)

    [LBAC_CACHE](LBAC_CACHE)

    [LBAC$SA_LABELS](LBAC$SA_LABELS)
    
- Logical standby database related

    [DBMS_LOGSTDBY](DBMS_LOGSTDBY)

- Distributed cluster management related

    [DBMS_CM](DBMS_CM)

- Anti-tamper capability related

  [HIST_CHECK](HIST_CHECK)

- Pass-through capability related

  [DBMS_LICENSE](DBMS_LICENSE)

- Partition table global index maintenance and cleanup

  [DBMS_PART](DBMS_PART)

Based on the above built-in advanced packages, users can perform real-time functionality operations in the SQL client. For specific functionality descriptions of each advanced package, refer to the relevant documentation. Users can also utilize [Package](../../All Manuals/Development Guide/PL Reference Manual/PL对象/自定义高级包) functionality for more customized development.

> **Note**:
> 
> For LSC table, built-in advanced packages are only allowed to be called within the procedure body.