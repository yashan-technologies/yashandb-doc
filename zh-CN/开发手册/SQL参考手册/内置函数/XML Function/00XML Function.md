XML处理函数（XML Function）是指对XML数据进行处理的一系列函数，使用此类函数的前提约束如下：

- 所有XML函数仅适用于单机/共享集群/分布式集群部署中的HEAP表。

- YashanDB服务端需安装xml插件。

    > **Note**:
    > 
    > 按照默认参数安装数据库服务时，默认已安装xml插件，可通过查看$YASDB_HOME/third路径下是否存在xml文件夹确认。
    >
    > 当从旧版本升级至YashanDB 23.4.2及以后时，不会自动安装xml插件，如需使用需要XML处理函数只能重装YashanDB。

在YashanDB中，XML处理函数包括：

[EXISTSNODE](EXISTSNODE)

[EXTRACTVALUE](EXTRACTVALUE)

[XMLAGG](XMLAGG)

[XMLELEMENT](XMLELEMENT)

[XMLEXTRACT](XMLEXTRACT)

[XMLPARSE](XMLPARSE)

[XMLSEQUENCE](XMLSEQUENCE)

[XMLTABLE](XMLTABLE)
