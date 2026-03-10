Database developers create various database objects within their privilege scope, such as tables, indexes, views, sequences, synonyms, triggers, and stored procedures. By assigning different users to different tablespaces, data can be physically isolated by business type. However, for objects within the same physical space, database administrators also need to establish a set of resource planning, daily monitoring, and alert handling mechanisms to ensure the stable and effective operation of the system. Among these, the management of tables is the most important because they serve as the carriers of business data and are the foundation for the operation of all other objects.

YashanDB provides a series of management views for DBAs to view and monitor all object-related information in the database:

- DBA_OBJECTS: Information about all objects.
- DBA_TABLES/DBA_PART_TABLES/DBA_TAB_PARTITIONS/DBA_PART_KEY_COLUMNS/DBA_TAB_SUBPARTITIONS/DBA_SUBPART_KEY_COLUMNS: Information about tables and table partitions, as well as secondary partitions.
- DBA_TRIGGERS: Information related to triggers.
- DBA_PROCEDURES: Information about stored procedures and UDFs.
- DBA_LOBS/DBA_LOB_PARTITIONS/DBA_LOB_SUBPARTITIONS: Information related to large objects.
- DBA_INDEXES/DBA_IND_COLUMNS/DBA_PART_INDEXES/DBA_IND_PARTITIONS/DBA_IND_SUBPARTITIONS: Information about indexes and index partitions, including secondary partitions.
- DBA_VIEWS: Information related to views.
- DBA_SYNONYMS: Information related to synonyms.
- DBA_SEQUENCES: Information related to sequences.