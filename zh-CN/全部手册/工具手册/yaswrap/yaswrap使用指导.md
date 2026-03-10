查看帮助
----

使用yaswrap -H命令可查看帮助信息。

```shell
$ yaswrap -H
```

查看版本
----

使用yaswrap -V命令可查看版本信息。

```shell
$ yaswrap -V
```

## 命令格式

```shell
$ yaswrap iname=input_file [oname=output_file] [keep_comments={yes|no}]
```
**iname**

需要进行包装加密的PL文本文件，支持指定相对路径。

input_file无后缀时，默认为其增加文件扩展名.sql，此时要求input_file.sql必须存在。

input_file文件名称（含后缀）总长度最大支持251个字符。

**oname**

输出的包装加密文件，支持指定相对路径。

该选项可以省略，则默认输出为输入文件路径下的input_file.plb。

output_file无后缀时，默认为其增加文件扩展名.plb。

**keep_comments**

指定是否保留其他注释，默认值为no。

- yes：只去除PL文本内的注释，保留其他注释。

- no：去除PL文本内的注释和其他注释。

## 字符集设置

yaswrap支持指定PL源码的字符集用于后续解密，由${YASDB_HOME}/client/yasc_env.ini中配置的客户端字符集参数CHARACTER_SET决定。

请确保PL源码字符集与客户端字符集一致，否则可能会出现无法解密的情况。

## 摘要算法设置

yaswrap加密时，支持选择摘要算法，由${YASDB_HOME}/client/yasc_env.ini中配置的客户端参数WRAP_ALOGRITHM决定。

可选范围为：SHA1、SM3，默认使用SHA1摘要算法。

> **Note**: 
>
> - 使用yaswrap加密PL源码时，如果input_file文件中存在部分已由yaswrap加密过的内容将直接跳过这部分密文（不进行重复加密），只对其余明文源码按照当前配置的摘要算法进行加密。
>
> - 如需采用SM3算法且需要使用OpenSSL工具时，请先参照[依赖项准备](../../安装和升级/安装部署/安装前准备/依赖项准备)检查并确保服务器系统中已安装符合要求的工具。
