YashanDB Python驱动（python-yaspy）是支持[Python DB API协议](https://peps.python.org/pep-0249/)的Python拓展模块，该模块可使通用Python应用程序直接连接YashanDB数据库。

仅当YashanDB部署为yashan模式时支持使用该驱动进行连接。

YashanDB提供两个python驱动包：yaspy、yasdb，安装其一后即可连接YashanDB数据库。

YashanDB v23.4.4对yaspy模块进行了性能提升（同等测试环境下，yaspy较yasdb有较大性能优势）。此外，后续新增功能的演进优先级为yaspy > yasdb，推荐使用**yaspy**模块。

本章节仅以yaspy为例，yasdb模块可进行参考。
