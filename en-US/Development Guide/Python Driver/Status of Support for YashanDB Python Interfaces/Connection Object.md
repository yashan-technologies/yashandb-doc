The connection object is used to represent and manage a single connection between a Python application and a database.

##  Object Creation Method

The connection object must be created using [connect\(\)](./python-yaspy Module) or [poolname.acquire\(\)](./Connection Pool Object).

##  Connection Methods

|Method | Description  |
| ---------- | ------ |
| Connection.close()    | Close the connection now. |
| Connection.commit()   | Commit any pending transaction. |
| Connection.rollback() | Roll back any pending transaction.  |
| Connection.cursor()   | Create a new [Cursor Object](./Cursor Object). |
