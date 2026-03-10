本文以使用Centos 7.3.1、go1.18 linux/amd64及以上版本和YashanDB客户端安装包yashandb-client-xx.xx-linux-x86_64.tar.gz为例，介绍YashanDB GO驱动在该环境下的安装配置过程。

## Go环境安装

使用YashanDB Go驱动的Go语言程序版本需在1.18及以上，开发环境为Linux  for x86_64，请至Golang官网下载。以go1.22.7为例：

![go-download-linux](./image/go-download-linux.png)

下载安装完成后，配置如下环境变量（以下路径为示例，请修改为实际安装路径）：

```shell
export GOROOT=/home/yashan/go1.22.7/go
export GOPATH=/home/yashan/go1.22.7/src
export PATH=$PATH:${GOROOT}/bin:${GOPATH}/bin
export GO111MODULE=on
export GOPROXY=https://goproxy.cn,direct
```

使用go version检验编译环境是否正常：

```shell
$ go version
go version go1.22.7 linux/amd64
```

## YashanDB Go驱动安装

### 步骤1：下载YashanDB客户端安装包

1. 参考[YashanDB软件包清单](../../../安装和升级/安装部署/安装前准备/下载软件包)获取YashanDB客户端安装包。
2. 将YashanDB客户端安装包下载并解压到本地路径，例如/home/yashan/client。

   安装包解压后可得到以下文件夹：

   * bin：客户端可执行文件（目前包括yasql）。
   * include：C驱动的头文件。
   * lib：C驱动的库文件。

### 步骤2：设置动态库依赖路径

将 `/home/yashan/client/lib`设置到Linux LD_LIBRARY_PATH中，具体操作为：

1. 编辑bashrc文件。

   ```shell
   vi ~/.bashrc
   ```
2. 新起一行增加LD_LIBRARY_PATH搜索路径。

   ```shell
   export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/yashan/client/lib
   ```
3. 保存并退出。
4. 刷新系统变量配置。

   ```shell
   source ~/.bashrc
   ```

### 步骤3：下载Go驱动安装包

1. 参考[YashanDB软件包清单](../../../安装和升级/安装部署/安装前准备/下载软件包)获取YashanDB Go驱动安装包。

2. 将Go驱动安装包下载并解压到本地路径 `/home/yashan/yasdb-go`。

### 步骤4：构建Go驱动客户端

1. 编写Go应用程序。

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

2. 加载依赖包，其中replace路径为YashanDB Go驱动的安装路径。

    ```shell
    $ go mod init example
    $ go mod edit -replace=git.yasdb.com/go/yasdb-go=/home/yashan/yasdb-go
    $ go mod tidy
    ```

3. 编译生成可执行程序。

    ```shell
    $ go build -o example  main.go
    ```

4. 运行程序。

    ```shell
    $ ./example
    instance version:  => Enterprise Edition Release xx.xx x86_64
    ```
