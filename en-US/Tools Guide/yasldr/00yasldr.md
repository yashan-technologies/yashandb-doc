*yasldr* is a data fast import tool provided by YashanDB, used to quickly import CSV format data files into the YashanDB database, supporting remote node imports via database connections.

Important notes before running this tool:
- When importing partition tables with yasldr, if the number of partitions exceeds 2000, the import performance will gradually decline as the number of partitions increases.

- yasldr supports importing multiple files for a single table, and supports using * and ? as wildcards to import multiple files, but does not support importing multiple tables simultaneously.
- yasldr supports two operational modes: file splitting and data importing. It operates in data import mode by default and does not support executing both modes simultaneously.
- The import process of yasldr is multi-threaded and parallel. If there are constraints on the target table, the import process may produce undefined behavior.
- yasldr does not support importing temporary tables, synonyms of tables, and views. Tables containing GIS columns only support BASIC mode import.
- yasldr defaults to operate in BATCH mode for imports; when importing duplicated tables, it will switch to BASIC working mode. BATCH mode does not compute DEFAULT expressions, and columns in the table to be imported without mapped data will be filled with NULL values. If DEFAULT values need to be computed for unmapped data columns, it is necessary to explicitly use BASIC mode for import.
- For LSC tables with mutable data, simultaneously importing multiple partition data may affect the data arrangement between partitions, which in turn may affect data order and query performance. When importing a large amount of data, it is recommended to import data between partitions serially, or to convert MCOL data to SCOL data as soon as possible.
