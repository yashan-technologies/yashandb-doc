This article provides two different usage examples using Centos 7.3.1 and go1.18 linux/amd64, with specific implementations as follows:

## Example One

1. Directly write the YashanDB go driver client program goexample1.go:

    ```go
    package main
    
    import (
        "database/sql"
        "fmt"
        "log"
    
        _ "git.yasdb.com/go/yasdb-go"
    )
    
    func Connect() *sql.DB {
        db, err := sql.Open("yasdb", "sales/sales@127.0.0.1:1688")
        if err != nil {
            log.Fatalf("some error %s", err.Error())
        }
        return db
    }
    
    var sql_1_row string = `select version from v$instance`
    
    func main() {
        db := Connect()
        a := ""
        rows, err := db.Query(sql_1_row)
        if err != nil {
            log.Fatal("query failed", err.Error())
        }
        for rows.Next() {
            err = rows.Scan(&a)
            if err != nil {
                log.Fatal("some wrong for query", err.Error())
            }
            fmt.Println(a)
        }
    }
    ```

2. Load dependency packages:

    ``` shell
    $ go mod init goexample1
    $ go mod edit -replace=git.yasdb.com/go/yasdb-go@v1.0.1=/home/  yasdb/GOExample/yasdb-go-22.1-linux-x86_64
    $ go mod tidy
    ```

3. Compile:

    ``` shell
    $ go build -o goexample1 goexample1.go
    ```

4. Execute:

    ``` shell
    $ ./goexample1
    ```

## Example Two

1. Combine with the third-party library sqlx to write the YashanDB go driver client program goexample2.go:

    ``` shell
    package main
    
    import (
        "database/sql"
        "fmt"
        "log"
    
        _ "git.yasdb.com/go/yasdb-go"
        "github.com/jmoiron/sqlx"
    )
    
    var db *sqlx.DB
    
    type user struct {
        Id   int    `db:"id"`
        Age  int    `db:"age"`
        Name string `db:"name"`
    }
    
    func InitDB() (err error) {
        dsn := "sales/sales@127.0.0.1:1688"
        db, err = sqlx.Open("yasdb", dsn)
        if err != nil {
            fmt.Printf("connect server failed, err:%v\n", err)
            return
        }
        db.SetMaxOpenConns(10)
        db.SetMaxIdleConns(3)
        return
    }
    
    func exec(sqlstr string, args ...interface{}) (sql.Result,  error) {
        fmt.Println(sqlstr)
        result, err := db.Exec(sqlstr, args...)
        if err != nil {
            log.Fatal(err)
        }
        return result, err
    }
    
    func Create() {
        exec("drop table if exists test_users")
        exec("create table test_users (id int PRIMARY KEY, name     varchar(256), age int)")
    }
    
    func Insert(id int, name string, age int) {
        exec("INSERT INTO test_users(id, name, age) VALUES(?, ?, ?) ", id, name, age)
    
    }
    
    func QueryMultiRow() {
        sqlStr := "SELECT id, age, name FROM test_users"
        var users []*user
        if err := db.Select(&users, sqlStr); err != nil {
            fmt.Printf("get data failed, err:%v\n", err)
            return
        }
        for i := 0; i < len(users); i++ {
            fmt.Printf("id:%d, name:%s, age:%d\n", users[i].Id,     users[i].Name, users[i].Age)
        }
    }
    
    func main() {
        if err := InitDB(); err != nil {
            log.Fatal(err)
        }
        Create()
        Insert(1, "Zhang San", 3)
        Insert(2, "Li Si", 4)
        Insert(3, "Wang Wu", 5)
        db.MustBegin().Commit()
        QueryMultiRow()
    }
    ```

2. Load dependency packages:

    ``` shell
    $ go mod init goexample2
    $ go mod edit -replace=git.yasdb.com/go/yasdb-go@v1.0.1=/home/  yasdb/GoExample/src
    $ go mod tidy
    ```

3. Compile:

    ``` shell
    $ go build -o goexample2  goexample2.go
    ```

4. Execute:

    ``` shell
    $ ./goexample2
    ```
