The YashanDB client provides the following functionality:

- Client connection management and SQL command line tool (*yasql*)
    
    The *yasql* tool can connect to the YashanDB server and execute various database operations by entering SQL commands.

- Date export tool (*exp*)

    The *exp* tool is a complementary export tool for YashanDB, providing the capability to export data in different ways for data migration or backup restoration.

- Data import tool (*imp*)

    The *imp* tool is a complementary import tool for YashanDB, providing the ability to import metadata in file format, including all metadata and data such as table structures, indexes, and constraints. It can be used for data migration or backup restoration between YashanDB databases.

- CSV data import tool (*yasldr*)

    The *yasldr* tool is a client import tool provided by YashanDB, capable of remote operation, used to execute data imports from CSV files. It can be used for data migration or backup restoration between heterogeneous databases.

- [C driver](../../../Development Guide/C Language Family Drivers/C Driver/00C Driver) interfaces

    You can use the C driver interface provided by YashanDB to develop applications in the C language that connect to YashanDB and perform various database operations.

The client supports installation on the following platforms:

- Windows: For specific installation operations, see [Windows Client Installation](./Windows Client Installation).

- Linux: For specific installation operations, see [Linux Client Installation](./Linux Client Installation).

- macOS: For specific installation operations, see [macOS Client Installation](./macOS Client Installation).