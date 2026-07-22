## Perl环境准备

通用Perl应用程序连接YashanDB需要先安装perl、DBI、DBD-ODBC，YashanDB Perl驱动对依赖项版本兼容信息如下：

- Perl：5.16.3及以上
- DBI：1.644及以上
- DBD-ODBC：1.61及以上

**下载并安装perl开发环境**

在官方路径中下载并安装符合上述要求版本的perl，自行配置perl环境。

**Linux平台验证perl环境**

配置完成后，通过运行perl --version验证Perl环境是否正常：

```shell
# perl --version
This is perl 5, version 16, subversion 3 (v5.16.3) built for x86_64-linux-thread-multi
```

**下载并安装DBI**

使用cpan DBI下载或在官方路径中下载并编译安装符合上述要求版本的DBI，自行配置DBI环境。

**下载并安装DBD-ODBC**

使用cpan DBD::ODBC下载或在官方路径中下载并编译安装符合上述要求版本的DBD::ODBC，自行配置DBD::ODBC环境。

## 安装依赖项

使用YashanDB Perl驱动需要先安装和配置YashanDB ODBC驱动库，操作过程请参考安装手册[YashanDB ODBC安装说明](../C语言系驱动/ODBC驱动/ODBC安装说明/00ODBC安装说明)章节。

## YashanDB Perl驱动包安装

Perl驱动无驱动包，完成以上perl、perl的DB模块和YashanDB ODBC驱动的安装即可使用perl对Yashan数据库进行操作。
