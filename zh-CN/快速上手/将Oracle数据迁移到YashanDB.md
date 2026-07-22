对于异构数据库迁移，YashanDB支持如下两种迁移方式。

- 数据迁移平台-YMP：YMP（YashanDB Migration Platform）是YashanDB提供的数据库迁移产品，支持数据源类型包括Oracle、MySQL、DM及YashanDB，支持元数据和数据一键迁移到崖山数据库。具体的适配版本可参考[YMP产品规格](https://doc.yashandb.com/ymp/23.4/zh/Product-Introduction/Specifications.html)。
- 第三方迁移平台：支持通过Kettle等第三方工具将Oracle、MySQL、DM等数据库迁移到YashanDB，相关操作可参考知识库[Kettle迁移MySQL到YashanDB](https://yashandb.com/newsinfo/7396987.html)、[Kettle迁移PostgreSQL到YashanDB](https://yashandb.com/newsinfo/7396988.html)。

本章节以YMP迁移小规格Oracle数据库到YashanDB为例介绍完整的迁移过程。迁移过程中，YMP会进行业务数据的暂存和处理，迁移数据量越大、迁移速度要求越高对部署YMP的服务器资源要求越高，因此本案例作为迁移示例，建议Oracle源数据库不超过30张表，单表数据量控制在百万级。对于生产环境数据迁移请参考[YMP数据迁移平台产品文档](https://doc.yashandb.com/ymp/23.4/zh/Product-Introduction/Summarize.html)进行操作。

## 迁移前准备

### YMP服务器配置要求

请按照如下要求准备YMP服务器环境。如果YashanDB服务器资源充裕，YMP可以和数据库部署在同一台服务器上进行使用。

|  环境配置项| 最低要求| 说明|
| :----------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| 操作系统     | CentOS 7.6以上、KylinOS V10                                  |                                                              |
| 处理器架构   | X86-64、ARM-64                                               | 本章节以X86-64位处理器为例进行介绍                           |
| CPU核数      | 4核及以上                                                    |                                                              |
| 可用内存     | 8G及以上                                                     |                                                              |
| 磁盘可用空间 | SSD，可用大小根据迁移表大小而定                              | 建议不小于待迁移表中的最大单表数据量的3倍                    |
| 开放端口     | 8090-8094                                                    | 可按最低要求开放端口，也可以通过关闭防火墙或配置防火墙白名单进行控制。 |
| JDK          | JDK8、JDK11、JDK17                                           | 1. YMP仅支持在JDK8、JDK11或JDK17的环境下安装使用。 </br>2.处理器架构为ARM-64，YMP所需的JDK版本须为JDK11及以上，能够有效解决可能出现的数据库连接慢问题。 |
| openssl      | 1.1.1                                                        | YMP服务器和目标端崖山数据库服务器的openssl版本需保持一致，如YMP和崖山数据库合设，可忽略此要求。 |
| 浏览器       | Google Chrome 88及以上；</br>Microsoft Edge 88及以上；</br>Firefox 78及以上 |                                                              |

### 源端数据库信息收集及准备

针对源端数据库需收集下列信息。

- 服务器IP及端口。
- 数据库服务名称。
- 登录连接用户名和密码。
- 源端数据库的登录连接用户需拥有待迁移schema的CONNECT、RESOURCE权限。

本次示例仅考虑迁移Oracle数据库中某一schema的业务数据，且该schema中的对象对其他schema中的对象没有依赖关系，如需全量迁移Oracle数据库，需参考[数据迁移平台-YMP](https://doc.yashandb.com/ymp/23.4/zh/Product-Introduction/Summarize.html)文档了解相关权限要求。

### 目标端数据库信息收集及配置

针对目标端YashanDB需收集下列信息。本章节使用的示例信息为[快速体验YashanDB](快速体验YashanDB.md)章节完成的配置示例，实际迁移时需根据真实环境信息调整相关参数。

- YashanDB23.4版本已支持兼容mysql模式，如需将源端MySQL迁移到YashanDB，需要。
- 服务器IP及端口。
- 数据库名称和Schema名称。
- 登录连接用户名和密码，该连接用户需拥有DBA权限。在[快速体验YashanDB](快速体验YashanDB.md)章节，已完成测试用户sales并赋权。

## 部署YMP

1. 检查环境配置，开放服务端口。

   ```bash
   # firewall-cmd --zone=public --add-port=8090-8094/tcp --permanent
   ```

2. 将JDK版本安装路径及信息配置到服务器环境变量中，以安装路径为/usr/tools/jdk8为例。

   ```bash
   # vi /etc/profile
   # export JAVA_HOME=/usr/tools/jdk8
   # export PATH=$JAVA_HOME/bin:$PATH
   # export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
   # source /etc/profile
   # java -version
   ```

3. 创建YMP安装用户，并进行赋权。YMP与数据库服务器合设时，需创建不同的安装用户。

   ```bash
   # useradd -d /home/ymp -m ymp
   # passwd ymp
   ```

4. 以ymp用户将[快速体验YashanDB](快速体验YashanDB.md)中部署的数据库安装包和YMP安装包yashan-migrate-platform-{版本号}-linux-x86-64.zip上传到/home/ymp路径，解压后执行安装命令。

   ```bash
   # su - ymp
   $ unzip yashan-migrate-platform-{version_number}-linux-x86-64.zip
   $ cd /home/ymp/yashan-migrate-platform/
   $ sh bin/ymp.sh install --db /home/ymp/yashandb-{version_number}-linux-x86_64.tar.gz
   $ sh bin/ymp.sh status
   YMP is running, pid is 7747.
   Built-in database is used, pid is 7617.
   ```

部署完成后，可通过 http://YMP_IP:PORT/ 访问YMP服务页面，PORT默认值为8090。首次登录时需要重置登录密码，初始用户名和密码为admin/admin。

## 开始迁移

1. 选择【数据源管理】页面，单击【添加数据源】按钮，依次将源端Oracle数据库和目标端YashanDB添加作为数据源，Oracle的连接配置需勾选**是否为普通用户**选项，崖山数据库配置连接用户可使用[快速体验YashanDB](快速体验YashanDB.md)章节创建的测试用户sales。
2. 选择【任务管理】页面，单击【创建任务】按钮，根据页面提示选择【评估】、【迁移】、【校验】体验完整流程，评估类型选择**在线评估**，配置源端和目标端数据库后进入【迁移任务】配置页面，根据页面提示完成配置后进行数据迁移，迁移完成后进行校验确认数据一致性。详细的迁移操作可参考[YMP快速开始](https://doc.yashandb.com/ymp/23.3/zh/Quick-Start/Quick-Start.html)。
