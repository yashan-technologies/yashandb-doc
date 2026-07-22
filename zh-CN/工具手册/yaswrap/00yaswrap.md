yaswrap是YashanDB的PL源代码包装加密工具，工具生成的包装加密后文本可在YashanDB数据库内正常执行。

yaswrap工具本身不会对输入文件中的语句进行语法检查，并通过create or replace statement和'/'判断PL的起始与终止。可以由yaswrap进行包装加密的PL对象有：

- Package head
- Package body
- Function
- Procedure

对于其他对象，例如trigger，可先将其封装进一个procedure，再通过procedure进行包装加密。

使用yaswrap工具依赖一些环境配置项（通常已在安装YashanDB时完成配置），具体的配置项如下：

|  配置项| 说明|
| --------------- | ------------------------------------------------------------ |
| LD_LIBRARY_PATH | yaswrap 依赖libyascli.so和libyas_infra.so，通常位于\${YASDB_HOME}/lib下，可设置如下环境变量：<br> export LD_LIBRARY_PATH=\${YASDB_HOME}/lib:${LD_LIBRARY_PATH} |
| PATH            | yaswrap 默认位置为\${YASDB_HOME}/bin下，可设置如下环境变量：<br> export PATH=\${YASDB_HOME}/bin:${PATH} |
