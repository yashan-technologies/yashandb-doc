This document takes Centos 7.3.1, go1.18 linux/amd64 and above versions, and the YashanDB client installation package yashandb-client-xx.xx-linux-x86_64.tar.gz as examples to illustrate the installation and configuration process of the YashanDB GO driver in this environment.

## Go Environment Installation

The Go language program version that uses the YashanDB Go driver must be 1.18 or above. The development environment is Linux for x86_64, and it can be downloaded from the Golang official website. For example, take go1.22.7:

![go-download-linux](./image/go-download-linux.png)

After the installation is complete, configure the following environment variables (the following paths are examples; please change them to the actual installation paths):

```shell
export GOROOT=/home/yashan/go1.22.7/go
export GOPATH=/home/yashan/go1.22.7/src
export PATH=$PATH:${GOROOT}/bin:${GOPATH}/bin
export GO111MODULE=on
export GOPROXY=https://goproxy.cn,direct
```

Use go version to check if the compilation environment is normal:

```shell
$ go version
go version go1.22.7 linux/amd64
```

## YashanDB Go Driver Installation

### Step 1: Download YashanDB Client Installation Package

1. From the [YashanDB Official Website Download Center](https://download.yashandb.com/download) or contact our technical support to obtain the corresponding software package.
2. Download and extract the YashanDB client installation package to a local path, for example, /home/yashan/client.

   After extracting the installation package, the following folders will be obtained:

   * bin: Client executable files (currently includes *yasql*).
   * include: Header files for the C driver.
   * lib: Library files for the C driver.

### Step 2: Set Dynamic Library Dependency Path

Set `/home/yashan/client/lib` to the Linux LD_LIBRARY_PATH. The specific operations are as follows:

1. Edit the bashrc file.

   ```shell
   vi ~/.bashrc
   ```
2. Add a new line to increase the LD_LIBRARY_PATH search path.

   ```shell
   export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/yashan/client/lib
   ```
3. Save and exit.
4. Refresh the system variable configuration.

   ```shell
   source ~/.bashrc
   ```

### Step 3: Download Go Driver Installation Package

1. From the [YashanDB Official Website Download Center](https://download.yashandb.com/download) or contact our technical support to obtain the corresponding software package.

2. Download and extract the Go driver installation package to the local path `/home/yashan/yasdb-go`.

### Step 4: Build Go Driver Client

1. Write a Go application.

    ```go
    package main

    import (
    	"database/sql"
    	"fmt"

    	_ "git.yasdb.com/go/yasdb-go"
    )

    func main() {
        dsn := "sys/Cod-2022@127.0.0.1:1688"
    	db, err := sql.Open("yasdb", dsn)
    	if err != nil {
    		fmt.Println(err)
    		return
    	}
    	defer db.Close()

    	rows, err := db.Query("select version from v$instance")
    	if err != nil {
    		fmt.Println(err)
    		return
    	}
    	for rows.Next() {
    		var version string
    		err = rows.Scan(&version)
    		if err != nil {
    			fmt.Println(err)
    			return
    		}
    		fmt.Println("instance version: ", "=>", version)
    	}
    }
    ```

2. Load the dependency package, where the replace path is the installation path of the YashanDB Go driver.

    ```shell
    $ go mod init example
    $ go mod edit -replace=git.yasdb.com/go/yasdb-go=/home/yashan/yasdb-go
    $ go mod tidy
    ```

3. Compile to generate the executable program.

    ```shell
    $ go build -o example  main.go
    ```

4. Run the program.

    ```shell
    $ ./example
    instance version:  => Enterprise Edition Release xx.xx x86_64
    ```
