A transaction is a unit of work that contains one or a group of SQL statements. After applying data modifications to the database, these SQL statements are either all committed to make the data modifications effective, or all rolled back to undo the data modifications. YashanDB assigns a globally unique transaction ID to each transaction as an identifier, and all YashanDB transactions meet the fundamental properties of database transactions, which comply with the ACID properties.

The description of ACID is as follows:

- Atomicity: All modifications within a transaction must either all take effect or none at all; partial effects are not allowed.

- Consistency: The data versions before and after the transaction are consistent. For example, when transferring 100 yuan from account A to account B, account A must decrease by 100 yuan while account B increases by 100 yuan. Even if any failure occurs, the transaction ensures that the total balance of accounts A and B remains equal.

- Isolation: When multiple transactions are executed concurrently, each transaction can operate independently, isolated from one another without interference. Depending on the isolation level, the observable behaviors of transactions can vary.

- Durability: Once a transaction is committed, its modifications to the data are permanent, meaning that even in the event of a failure, the data modifications will not be lost.

YashanDB has designed a high-performance, high-concurrency transaction engine while satisfying the basic properties of transactions.

- [Data Concurrency Control and Consistency](Data Concurrency Control and Consistency): This section introduces how YashanDB maintains the consistency of the database.

- [Transaction Management](Transaction Management): This section introduces the definition and types of transactions, as well as how to manage transactions.