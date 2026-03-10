分布式数据库采用Shared-Nothing架构，根据业务表定义的分布键对数据进行分区划分，将数据水平划分到多个独立的存储实例中（DN节点组）。

通常情况下，业务应用不需要感知数据分布，业务数据访问请求统一提交到接入层（CN节点组）， 由接入层根据数据分布路由表进行请求的转发和汇聚计算。

部分业务应用对于数据访问时延有极致要求的场景，在业务架构支持完美Sharding能够保证不存在跨分区事务的前提下，YashanDB的JDBC驱动通过构建ShadingKey提供了数据分布感知能力，以支持业务应用对数据分片的直连访问，降低业务应用的数据访问时延。其中，DDL操作必须通过连接接入层（CN节点组）进行操作，以保证所有节点的元数据一致性。

## 相关接口

YashanDB JDBC中存在如下ShardingKey功能相关的接口：

|  类| 返回类型| 方法| 参数| 说明|
|-----| ------ | -------------- |---------------------------------------------------|--------------------------------------------------------------|
| YasDataSource | ShardingKeyBuilder | createShardingKeyBuilder() | | 创建ShardingKeyBuilder，ShardingKeyBuilder用来构建ShardingKey的分布键。  |
| YasDataSource | ConnectionBuilder | createConnectionBuilder() | | 创建ConnectionBuilder，ConnectionBuilder用来添加ShardingKey信息，然后连接。 |
| ShardingKeyBuilder | ShardingKeyBuilder | subkey(Object subkey, SQLType subkeyType) | \[1\] subkey：分布键的值。<br/>\[2\] subkeyType：分布键的类型。<br/> | ShardingKey添加分布键。|
| ShardingKeyBuilder | ShardingKey | build() |  | 添加完分布键以后，构建ShardingKey。                                      |
| ConnectionBuilder | ConnectionBuilder | shardingKey(ShardingKey shardingKey) | \[1\] shardingKey：ShardingKey相关信息。<br/> | 添加ShardingKey相关信息。 |
| ConnectionBuilder | Connection | build() | | 获取数据所在数据库分片连接。 |

## 关于分布键的构建

数据分布感知需要业务应用识别并构建数据分区键。应用程序使用ShardingKey和ShardingKeyBuilder接口来构建数据分区键。分区键可由一个或多个字段组成，可参考[CREATE TABLE](../../SQL参考手册/SQL语句/CREATE TABLE)，在构建分区键时给定的字段顺序以及字段数据类型必须与表定义分区键保持一直。其中数据类型由java.sql.JDBCType来定义。

## 关于路由刷新

当集群发生扩缩容时，YashanDB会根据数据均衡策略对数据以数据分片为单位进行数据的重新分布，因此数据路由会发生变化。此时，通过ShardingKey获取到的连接会重新计算数据的分布节点，并自动的切换到新的节点上。如果切换不成功则会报错。

## 使用示例

ShardingKey的使用示例如下：

```java
    public static void main(String[] args) throws Exception {

        String className = "com.yashandb.jdbc.Driver";
        //配置CN节点地址列表
        String url = "jdbc:yasdb:loadBalance://192.168.24.32:1688,192.168.24.33:1688/yashan?poolTimeout=180&failover=on&failoverType=session";
        String user = "user"; //用户名
		String password = "password";//密码
        Class.forName(className);
        
        YasDataSource dataSource = new YasDataSource();
        dataSource.setURL(url);
        dataSource.setUser(user);
        dataSource.setPassword(password);
        
        //配置开启DN组内的TAF能力
        properties properties = new Properties();
        properties.setProperty("heartbeatSwitch","OFF");
        properties.setProperty("failover","ON");       
        properties.setProperty("failoverType","SELECT");
        dataSource.setConnectionProperties(properties);
        
        Connection connection2 = dataSource.getConnection();//不给定ShardingKey时，直接获取的CN连接
        Statement statement2 = connection2.createStatement();
        statement2.execute("CREATE TABLE T1 (id varchar(20) NOT NULL, name varchar(20) NOT NULL, age int) partition by hash(id, name) partitions auto tablespace set USERS"); 
        statement2.close();
        connection2.close();  
        
        //DML的访问使用数据分布感知能力，对数据分片进行直连访问
        ShardingKey shardingKey = dataSource.createShardingKeyBuilder()
                                    .subkey(100, JDBCType.INTEGER)
                                    .subkey("ID-001", JDBCType.VARCHAR)
                                    .build();
        Connection connection1 = dataSource.createConnectionBuilder().shardingKey(shardingKey).build();
        Statement statement1 = connection1.createStatement();
        ResultSet resultSet = statement1.executeQuery("select * from T1 where id = 100 and name = 'ID-001'"); 
        resultSet.close();
        statement1.close();
        connection1.close();
    }
```
