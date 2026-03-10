The distributed database uses a Shared-Nothing architecture. It partitions data based on the distribution key defined by the business table, horizontally dividing the data into multiple independent storage instances (DN node groups). 

Typically, business applications do not need to be aware of data distribution. Business data access requests are uniformly submitted to the access layer (CN node groups), which forwards requests and aggregates calculations based on the data distribution routing table. 

In some scenarios where business applications have extreme requirements for data access latency, YashanDB's JDBC driver provides data distribution awareness capabilities by constructing the ShardingKey. This supports direct access to data shards for business applications, reducing data access latency, provided that the business architecture supports perfect sharding to ensure no cross-partition transactions exist. DDL operations must be conducted through the connection to the access layer (CN node groups) to ensure metadata consistency across all nodes.

## Related Interfaces

The following interfaces related to ShardingKey functionality exist in YashanDB JDBC:

|Class |Return Type |Method |Parameters |Description |
|-----| ------ | -------------- |---------------------------------------------------|--------------------------------------------------------------|
| YasDataSource | ShardingKeyBuilder | createShardingKeyBuilder() | | Creates a ShardingKeyBuilder, which is used to construct the distribution key of the ShardingKey. |
| YasDataSource | ConnectionBuilder | createConnectionBuilder() | | Creates a ConnectionBuilder, which is used to add ShardingKey information and then connect. |
| ShardingKeyBuilder | ShardingKeyBuilder | subkey(Object subkey, SQLType subkeyType) | \[1\] subkey：The value of the distribution key.<br/>\[2\] subkeyType：The type of the distribution key. | Adds a distribution key to the ShardingKey.                  |
| ShardingKeyBuilder | ShardingKey | build() | | Constructs the ShardingKey after adding the distribution keys. |
| ConnectionBuilder | ConnectionBuilder | shardingKey(ShardingKey shardingKey) | \[1\] shardingKey：Relevant information of the ShardingKey. | Adds information related to the ShardingKey.                 |
| ConnectionBuilder | Connection | build() | | Retrieves the database shard connection where the data is located. |

## About Constructing the Distribution Key

Data distribution awareness requires business applications to identify and construct the data partition key. Applications use the ShardingKey and ShardingKeyBuilder interfaces to construct the data partition key. The partition key can consist of one or more fields, which can be referenced in [CREATE TABLE](../../SQL Reference Manual/SQL Statements/CREATE TABLE). The order of the fields and their data types provided during the construction of the partition key must remain consistent with the table's defined partition key. The data type is defined by java.sql.JDBCType.

## About Route Refresh

When the cluster scales up or down, YashanDB redistributes data on a shard-by-shard basis according to data balancing strategies, leading to changes in data routing. At this time, the connections obtained through the ShardingKey will recalculate the distribution nodes and automatically switch to the new nodes. If the switch fails, an error will be reported.

## Usage Example

The usage example of ShardingKey is as follows:

```java
    public static void main(String[] args) throws Exception {

        String className = "com.yashandb.jdbc.Driver";
        // Configuring the CN node address list
        String url = "jdbc:yasdb:loadBalance://192.168.24.32:1688,192.168.24.33:1688/yashan?poolTimeout=180&failover=on&failoverType=session";
        String user = "user"; // Username
        String password = "password"; // Password
        Class.forName(className);
        
        YasDataSource dataSource = new YasDataSource();
        dataSource.setURL(url);
        dataSource.setUser(user);
        dataSource.setPassword(password);
        
        // Configuring to enable TAF capability within the DN group
        properties properties = new Properties();
        properties.setProperty("heartbeatSwitch","OFF");
        properties.setProperty("failover","ON");       
        properties.setProperty("failoverType","SELECT");
        dataSource.setConnectionProperties(properties);
        
        Connection connection2 = dataSource.getConnection(); // Obtaining CN connection directly without specifying ShardingKey
        Statement statement2 = connection2.createStatement();
        statement2.execute("CREATE TABLE T1 (id varchar(20) NOT NULL, name varchar(20) NOT NULL, age int) partition by hash(id, name) partitions auto tablespace set USERS"); 
        statement2.close();
        connection2.close();  
        
        // DML access using data distribution awareness capabilities for direct access to data shards
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
