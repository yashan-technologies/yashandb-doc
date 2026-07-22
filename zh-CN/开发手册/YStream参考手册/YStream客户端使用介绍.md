## 获取YStream客户端安装包

YashanDB YStream对JDK版本兼容信息如下：

- JDK：1.8及以上
- JRE：1.8及以上

### 方式一：通过Maven安装YStream客户端

YStream已发表在[The Maven](https://central.sonatype.com/artifact/com.yashandb/Ystream)上，具有以下Group ID和artifactId：

- Group ID: com.yashandb
- artifactId: Ystream

::: tabs
== 在pom.xml文件中添加依赖项：

```xml
<dependency>
    <groupId>com.yashandb</groupId>
    <artifactId>Ystream</artifactId>
    <version>x.y.z</version>
</dependency>
```

== 在build.gradle文件中添加依赖项：

```groovy
dependencies {
    implementation("com.yashandb:Ystream:x.y.z")
}
```
:::

> **Note**:
>
> x.y.z表示可用的YStream版本号，例如1.9.27。

### 方式二：离线安装YStream客户端

从[YashanDB官网下载中心](https://download.yashandb.com/download)，或者联系我们的技术支持获取对应的软件包。

## 连接YStream服务端

### 创建YStream客户端配置类

YStream提供一系列柯里化（Currying）构造方法，用于生成连接配置。

```java
YstreamConfig.<String>builder().build()
```

参数说明如下表所示。

|  参数| 必填/选填| 描述| 
|-----------------------|-------|-----------------------------|
| serverName            | 必填    | 数据库实例所在服务器IP地址或域名。                   |
| port                  | 必填    | 数据库服务端口。            |
| user                  | 必填    | 数据库用户名。                  |
| password              | 必填    | 数据库用户密码。                       |
| database              | 必填    | 数据库名称（保留参数，但不校验内容）。                 |
| deserializer          | 必填    | 反序列化器实现，用于指定YStream客户端输出的内容，提供默认实现DefaultDeserializer。 |
| startMode             | 选填    | 启动模式，分为第一次启动（StartMode.START）和断点续传（StartMode.RECOVER），默认值为StartMode.START。                |
| queueSize             | 选填    | YStream客户端内置阻塞队列的长度，获取增量逻辑日志时直接从该队列获取，默认值为128。                   |
| pollTimeout           | 选填    | 从阻塞队列中获取下一个结果的超时时间（单位：秒），默认值为10。                                |
| clientResponseTimeout | 选填    | YStream服务端等待YStream客户端响应的最长时间（单位：秒），默认值为60。                  |
| recoverPosition       | 选填    | StartMode.RECOVER模式必填，用于指定断点续传的点位。           |
| enableSequenceNextVal | 选填    | Sequence解析开关，设置为true时可解析YstreamSeqHwm LCR。默认值为false。|

### 实现Deserializer接口

YStream客户端提供将逻辑日志的内容反序列化为调用者指定类型的接口，调用者可根据实际情况自行实现该接口或直接使用YStream客户端提供的接口。

### 与YStream服务端建立连接

YStream客户端提供如下方法建立与YStream服务端的连接：

```java
YstreamClientBoot.getClient().open(YstreamConfig<T> ystreamConfig)
```

示例

```java
public static YstreamClientBoot getClient() throws YstreamException {
  // 获取YStream客户端实例
  YstreamClientBoot<String> client = YstreamClientBoot.getClient();
  // 创建YStream客户端配置类
  YstreamConfig<String> config = YstreamConfig.<String>builder()
      .setHost(HOST)
      .setPort(PORT)
      .setUser(USER)
      .setPassword(PASSWORD)
      .setServerName(CONNECTION_NAME)
      .setDeserializer(new StringDeserializer())
      .build();
  // 创建与YStream服务端的连接
  client.open(config);
  return client;
}
```

## 获取增量逻辑日志

调用YStream客户端的next方法获取增量逻辑日志。

```java
client.next();
```

## 设置已提交的position

每一条逻辑日志都带有一个唯一且递增的position。逻辑日志的position不会随着进程重启而变化，可以作为断点续传的接续点。

YStream客户端收到增量逻辑日志并确定无需再使用该逻辑日志后（例如该逻辑日志已在目标端应用提交），可以调用setAppliedPosition方法标记已经提交的逻辑日志位置，以便数据库推进YStream重启恢复点，清理无用的归档日志。若长时间未调用setAppliedPosition更新applied position，会导致数据库无法推进YStream重启恢复点，无法及时清理无用的归档日志。

```java
client.setAppliedPosition(appliedPosition);
```

## 关闭YStream服务端连接

当不再需要获取增量逻辑日志时，可调用YStream客户端的close方法关闭与YStream服务端的连接。

```java
client.close();
```
