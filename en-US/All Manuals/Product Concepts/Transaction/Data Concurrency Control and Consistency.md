In order to make full use of system resources (memory, CPU, network, etc.), YashanDB allows multiple sessions to access and modify the database content in parallel. Without control over concurrent operations, the integrity and consistency of the database may be compromised.

YashanDB maintains the database's consistency through multi-version concurrency control, transaction isolation levels, and locks:

- Multi-Version Concurrency Control: Primarily handles concurrency between reads and writes.
- Transaction Isolation Levels: Controls concurrency between multiple transactions, allowing concurrent transactions to access only the corresponding visible version of the data at different isolation levels.
- Locking Mechanism: Mainly handles concurrency between writes, controlling concurrent modifications to the same data by different transactions through the locking mechanism.

## Multi-Version Concurrency Control

### Read Consistency

YashanDB achieves read consistency through data multi-versioning. When modifying data, it retains historical versions of the data in the UNDO tablespace, allowing reads and writes to not block each other. Concurrent transactions can access consistent versions, with the following characteristics:

- Query Consistency: The data versions retrieved through SQL statements executed by users are consistent, visible, and already committed.
- Read and Write Not Blocking: When users execute SQL statements to modify data, it does not block concurrent transactions from querying the data being modified.

YashanDB uses the system change number (SCN) as a basis for transaction visibility judgment. SCN is a time-related value that advances when a transaction is committed. SQL statements query with a specific SCN as a perspective to determine the visibility of already committed transactions to the current query, thus obtaining consistent results.

The query statements access data in units of Block (data block/page) by judging the transaction visibility corresponding to the Xslot (transaction slot) on the Block:

-  For visible transactions, a consistent read Block, also known as a CR (Consistent Read) Block, is generated.
- For invisible transactions, the visible version is restored from the historical records in the rollback segment pointed to by the Xslot.

![](./image/MVCC.png)

Taking a HEAP block as an example, if there are 4 rows in the current Block, and transactions corresponding to row2 and row4 are not visible to the current query SCN, the corresponding visible versions can be found via the undo row pointed to by the Xslot.

- row2: It needs to apply one historical version to obtain a visible version.
- row4: Its visible historical version does not exist (the undo of the insert means the row is newly inserted and is not visible to the current query).

By applying the undo records to the HEAP block, a CR block visible to the query is generated, thereby satisfying the query's consistency. At this time, concurrent transactions can still access and modify the records on the page, which does not affect the current statement's access to the CR block.

All deployment forms of YashanDB meet read consistency, where a block in a YAC can be accessed and modified simultaneously by multiple instances, potentially leading to multiple instance transactions participating in generating the same HEAP block's CR block. The entire process completes in the global buffer.

**Statement-Level Read Consistency**

Users obtain the SCN based on a specific point in time when executing SQL queries, using this SCN for consistent reads during the query process.

The default multi-version read consistency in YashanDB is statement-level.

**Transaction-Level Read Consistency**

Transaction-level read consistency, based on the principle of statement-level read consistency, obtains the query SCN for each query statement from the snapshot of the current transaction start, meaning all statements within the same transaction access the same version of the data.

### Write Consistency

Write consistency defines that two (or more) concurrently executed statements need to be executed in an approximately serialized manner. Essentially, when concurrent modification statements affect each other, the later one will trigger a statement restart. In some scenarios where modification consistency needs to be maintained, YashanDB automatically executes in a write-consistent manner.

For example, without write consistency, the following concurrent statements may have update loss issues:

|Session 1 |Session 2 |Session 3 |Interpretation |
| --------------- | -------- | ----------- | ---------------------------- |
| create table employee (id int, title int, age int)<br/>partition by range (title) (<br/>partition p1 values less than (5),<br/>partition p2 values less than (10),<br/>partition p3 values less than (15)<br/>) enable row movement;<br/><br/>insert into employee values (1, 4, 24);<br/>insert into employee values (2, 8, 28);<br/>insert into employee values (3, 12, 30);<br/>commit; |           |           | Session 1 creates an employee test table and inserts three records. |
|           | select * from employee where id = 2 for update; |           | Locks the data in partition 2 using **for update** statement. |
|           |           | update employee set age = age + 1; | Increases the age of all employees by 1; session 3 will be blocked by session 2 and has to wait for session 2 to finish. |
| update employee set title = 6 where id = 3;<br/>commit; |           |           | Updates the title of the employee with id 3 from 12 to 6 and commits. This creates **cross-partition changes**; the information of employee 3 moves from partition 3 to partition 2. |
|           | rollback; | Update successful | Session 2 releases the lock, and session 3 updates successfully. |
|           |           | select * from employee; | Without the protection of write consistency, the age of employee 3 will have update loss. |

Write consistency defines the relationships between statements that are executed concurrently, while the relationships between concurrent transactions can be found in [Transaction Isolation Levels](#isolevel).

<span id="isolevel" name="isolevel" class="yaslink"></span>

## Transaction Isolation Levels

The concurrency of database transactions may have certain effects on the reads and writes between transactions:

- Dirty Read: One transaction reads data modified by another uncommitted transaction.
- Non-repeatable Read: Within the same transaction, multiple statements read the same row of data, and the read **data** changes.
- Phantom Read: Within the same transaction, multiple statements read data that meets the same condition, and the read **result set size** changes.

Transaction isolation levels ensure the behavior of concurrent execution of multiple transactions, affecting data consistency and concurrency performance. The ANSI standard defines four transaction isolation levels:

- Read Uncommitted (Read Uncommitted)

  The lowest level of isolation, performing well but can violate data consistency.
  
  At this level, dirty reads are allowed, meaning one transaction may see changes made by other concurrently executing uncommitted transactions.

- Read Committed (Read Committed)

  This isolation level guarantees that when a transaction accesses data modified by other transactions, it can only read committed data versions. It avoids dirty reads, but non-repeatable reads may still occur.

  This level also includes Current Committed, where only committed data versions can be read; there are no dirty reads or phantom reads, but it cannot guarantee read consistency within statements, and non-repeatable read scenarios may occur.

- Repeatable Read (Repeatable Read)

  Based on read committed, all statements in the same transaction see the same data versions, thus avoiding dirty reads and non-repeatable reads, although phantom reads can still occur.

- Serializable (Serializable)

  The strictest isolation level, transactions are completely isolated from each other, ensuring that concurrent transactions do not conflict, avoiding dirty reads, non-repeatable reads, and phantom reads.

Different isolation levels can lead to the following problems during concurrent data access:

|Isolation Level |Dirty Read |Non-repeatable Read |Phantom Read |
| -------- | ------ | ---------- | ------ |
| Read Uncommitted | Possible   | Possible             | Possible      |
| Read Committed   | Impossible | Possible             | Possible      |
| Repeatable Read  | Impossible | Impossible           | Possible      |
| Serializable      | Impossible | Impossible           | Impossible    |

YashanDB supports transaction isolation levels of Read Committed and Serializable.

### Read Committed

YashanDB defaults to the Read Committed isolation level, and it can also be set to Read Committed through SQL statements.

```sql
SET TRANSACTION isolation LEVEL read committed;
```

**Read Consistency**

Each statement in the transaction strictly executes according to statement-level read consistency. At the beginning of a statement, it obtains the latest system SCN as the query SCN, and uses the same SCN for querying throughout the execution of the statement, generating a consistent result set.

**Write Conflicts**

In write conflict scenarios, one transaction attempts to modify rows updated by another uncommitted transaction, triggering [row lock](#rowlock) waiting until the other transaction ends:

- If the waiting transaction rolls back, the current transaction will continue to lock the current row and proceed with the modification.
- If the waiting transaction commits, the current transaction will read the latest version and perform a condition check; if it meets the conditions, it will continue to lock the current row and proceed with the modification.

An example illustrates the Read Committed isolation level:

|Session 1 |Session 2 |Interpretation |
| -------------------- | --------------------- | ------------------------- |
| create table t1 (id int,name CHAR(10));<br/>insert into t1 values (1,'Tom');<br/>insert into t1 values (2, 'Jack');<br/>commit; |                     | Session 1 creates a test table and inserts two records.   |
|                          | set transaction isolation level read committed;<br/>select * from t1; | Session 2 sets the current transaction isolation level to Read Committed and queries table data. |
| update t1 set id = -1 where id = 1;<br/>commit;          |                    | Session 1 updates the id of the first record to -1 and commits.  |
|                          | select * from t1;      | Session 2 queries table data again and reads the updated value from session 1. |
| update t1 set name = 'Anna' where id = 2;         |                    | Session 1 updates the name of the second record to Anna.         |
|                          | update  t1 set name = 'John' where id = 2;                    | Session 2 attempts to update the same record (but cannot use the fields updated by Session 1 as condition queries), resulting in transaction waiting. |
| commit;       |                  | After Session 1 commits, Session 2's update succeeds.  |
|                          | select * from t1;          | The query result for session 2 is: <br/>-1   Tom<br/>2   John                        |

### Serializable

YashanDB's support for serializable is snapshot-based serialization, providing transaction-level read consistency capabilities and a write-write serialization conflict detection mechanism. You can set the isolation level to Serializable with the following SQL statement:

```sql
SET TRANSACTION isolation LEVEL serializable;
```

**Read Consistency**

Each statement in the transaction strictly follows transaction-level read consistency. At the start of the transaction, the current system SCN is obtained as the current transaction's query SCN. Throughout the entire execution of the serializable transaction, the same SCN is used for querying, generating consistent result sets.

**Write Conflicts**

The write conflict detection mechanism of Serializable differs from that of Read Committed:

- If the waiting transaction rolls back, the current transaction continues to lock the current row and modify it.
- If the waiting transaction commits, a serialization write conflict will be triggered leading to a serialization conflict error.

An example illustrates the Serializable isolation level:

|Session 1 |Session 2 |Interpretation |
| -------------------- | --------------------------- | ----------------------------- |
| create table t1 (id int);<br/>insert into t1 values (1);<br/>insert into t1 values (2);<br/>commit; |                          | Session 1 creates a test table and inserts two records.  |
|                                  | set transaction isolation level serializable;<br/>select * from t1; | Session 2 sets the current transaction isolation level to Serializable and queries table data. |
| update t1 set id = -1 where id = 1;<br/>commit;              |                             | Session 1 updates the id of the first record to -1 and commits.  |
|                                  | select * from t1;           | Session 2 queries table data again and reads the value before session 1's update. |
|                                  | update t1 set id = id * 10 where id = -1; | Session 2 update errors, detecting a serialization conflict error. |

<span id="lock" name="lock" class="yaslink"></span>

## Locking Mechanism

Locks are a mechanism within the database that control concurrent transactions' modifications to data, which consists of both metadata and user data. Concurrency based on data categories has several types:

- Conflicting modifications to metadata by concurrent transactions, i.e. DDL concurrency.
- Conflicting modifications to user data by concurrent transactions, i.e. DML concurrency.
- Conflicting modifications between user data and metadata by concurrent transactions, i.e. DDL and DML concurrency.

Different granularities of locks are used for concurrent control in the above scenarios. In YashanDB, the locks available to users are primarily table locks and row locks.

### Table Lock Management

Table locks mainly occur during DDL statements or DML statements that modify data. The lock is automatically applied during the statement execution and is released at the end of the transaction. There are two types of table lock modes:

- Share Lock (table-level shared lock, S): The lowest level of table lock that allows concurrent DML execution. When DML modifies data, a table-level shared lock is applied to block the execution of concurrent DDL.
- Exclusive Lock (table-level exclusive lock, X): The highest level of table lock, applied during DDL operations, blocking other concurrent DDL and DML executions.

You can explicitly add an exclusive lock to a target table using the statement "lock table employee in exclusive mode;".

<span id="rowlock" name="rowlock" class="yaslink"></span>

### Row Lock Management

Row locks primarily occur when DML statements modify data, locking the rows to be modified during the transaction. In YashanDB, row locks are a type of physical lock, registered through the Xslot (transaction slot) on the Block.

There is only one type of row lock, which is exclusive lock; row-level shared locks are not supported.

You can explicitly lock the rows to be accessed as follows:

```sql
SELECT * FROM employee FOR UPDATE;
```

### Deadlock and Detection

When multiple transactions acquire and modify the same database resource, resource waiting occurs (e.g., waiting for table lock release, waiting for row lock release, etc.). When multiple transactions wait for each other to release resources, a deadlock occurs. At this point, concurrent transactions alone cannot identify and resolve the deadlock; YashanDB supports detecting and handling transactions that cause deadlocks.

**Deadlock with Table Locks**

Taking explicit table locks as an example, a deadlock scenario is constructed:

|Session 1 |Session 2 |Interpretation |
| ---------------------------- | --------------------- | -------------------------- |
| create table t1 (id int);<br/>create table t2 (id int); |                                  | Create test tables t1 and t2.  |
| lock table t1 in exclusive mode; |                                  | Session 1 applies an exclusive lock to table t1. |
|                                 | lock table t2 in exclusive mode; | Session 2 applies an exclusive lock to table t2. |
| lock table t2 in exclusive mode; |                                  | Session 1 attempts to apply an exclusive lock to table t2, waiting for session 2's transaction. |
|                                 | lock table t1 in exclusive mode; | Session 2 attempts to apply an exclusive lock to table t1, waiting for session 1's transaction. |
|                                 |                                  | At this point, the database detects that sessions 1 and 2 are waiting for each other, reports a deadlock error, and resolves the deadlock. |

**Deadlock with Row Locks**

Taking an update transaction as an example, a row lock deadlock scenario is constructed:

|Session 1 |Session 2 |Interpretation |
| ---------------------------- | ------------- | ---------------------------- |
| create table t (id int);<br/>insert into t values (1);<br/>insert into t values (2);<br/>commit; |                          | Create a test table and insert two rows.  |
| update t set id = -id where id = 1; |                          | Session 1 updates row1.   |
|                                 | update t set id = id * 10 where id = 2; | Session 2 updates row2. |
| update t set id = -id where id = 2; |                          | Session 1 attempts to lock row2 while updating and waits for session 2's transaction. |
|                                 | update t set id = id * 10 where id = 1; | Session 2 attempts to lock row1 while updating and waits for session 1's transaction. |
|                                 |                                  | At this point, the database detects that sessions 1 and 2 are waiting for each other, reports a deadlock error, and resolves the deadlock. |