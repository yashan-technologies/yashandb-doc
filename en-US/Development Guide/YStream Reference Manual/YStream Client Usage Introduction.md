## Obtain YStream Client Installation Package

The compatibility information for the YashanDB YStream client with JDK versions is as follows:

- JDK: 1.8 and above
- JRE: 1.8 and above

### Method 1: Install YStream Client via Maven

YStream is already published on [The Maven](https://central.sonatype.com/artifact/com.yashandb/Ystream) and has the following Group ID and artifactId:

- Group ID: com.yashandb
- artifactId: Ystream

::: tabs
== Add the dependencies to the pom.xml file:

```xml
<dependency>
    <groupId>com.yashandb</groupId>
    <artifactId>Ystream</artifactId>
    <version>x.y.z</version>
</dependency>
```

== Add the dependencies to the build.gradle file:

```groovy
dependencies {
    implementation("com.yashandb:Ystream:x.y.z")
}
```
:::

> **Note**:
>
> x.y.z represents the available ystream version number, e.g., 1.9.27.

### Method 2: Offline Installation of YStream Client

From the [YashanDB Official Website Download Center](https://download.yashandb.com/download), or contact our technical support to obtain the corresponding software package. 

## Connect to YStream Server

### Create YStream Client Configuration Class

YStream provides a series of currying constructors to generate connection configurations.

```java
YstreamConfig.<String>builder().build()
```

Parameter descriptions are shown in the table below.

|Parameter |Required/Optional |Description | 
|-----------------------|-------|-----------------------------|
| serverName            | Required           | The IP address or domain name of the database instance server.                   |
| port                  | Required           | The database service port.                              |
| user                  | Required           | The database username.                                  |
| password              | Required           | The database user password.                             |
| database              | Required           | The database name (reserved parameter, content is not validated).                |
| deserializer          | Required           | Deserializer implementation to specify the content output by the YStream client, providing a default implementation DefaultDeserializer. |
| startMode             | Optional           | Start mode, divided into first start (StartMode.START) and resume from breakpoint (StartMode.RECOVER), default value is StartMode.START. |
| queueSize             | Optional           | The length of the YStream client's built-in blocking queue, which retrieves incremental logical logs directly from this queue, default value is 128. |
| pollTimeout           | Optional           | Timeout period (in seconds) for retrieving the next result from the blocking queue, default value is 10. |
| clientResponseTimeout | Optional           | The maximum wait time (in seconds) for the YStream server to wait for a response from the YStream client, default value is 60. |
| recoverPosition       | Optional           | Required in StartMode.RECOVER mode, used to specify the point for resuming from a breakpoint. |
| enableSequenceNextVal | Optional           | Sequence parsing switch; if set to true, can parse YstreamSeqHwm LCR. Default value is false. |

### Implement the Deserializer Interface

The YStream client provides an interface to deserialize the content of logical logs into the type specified by the caller. The caller can implement this interface as needed or directly use the interfaces provided by the YStream client.

### Establish Connection with YStream Server

The YStream client provides the following method to establish a connection with the YStream server:

```java
YstreamClientBoot.getClient().open(YstreamConfig<T> ystreamConfig)
```

***Example***

```java
public static YstreamClientBoot getClient() throws YstreamException {
  // Get YStream client instance
  YstreamClientBoot<String> client = YstreamClientBoot.getClient();
  // Create YStream client configuration class
  YstreamConfig<String> config = YstreamConfig.<String>builder()
      .setHost(HOST)
      .setPort(PORT)
      .setUser(USER)
      .setPassword(PASSWORD)
      .setServerName(CONNECTION_NAME)
      .setDeserializer(new StringDeserializer())
      .build();
  // Create a connection to the YStream server
  client.open(config);
  return client;
}
```

## Retrieve Incremental Logical Logs

Call the next method of the YStream client to retrieve incremental logical logs.

```java
client.next();
```

## Set the Committed Position

Each logical log carries a unique and incremental position. The position of the logical log does not change with process restarts, making it a suitable continuation point for resuming from a breakpoint.

After the YStream client receives incremental logical logs and determines that the logical logs are no longer needed (for example, they have been committed at the target end), it can call the setAppliedPosition method to mark the position of the logical logs that have been committed. This allows the database to advance the YStream restart recovery point and clean up unnecessary archive log files. Failing to call setAppliedPosition to update the applied position for an extended period may cause the database to be unable to advance the YStream restart recovery point and to clean up unnecessary archive log files in a timely manner.

```java
client.setAppliedPosition(appliedPosition);
```

## Close Connection to YStream Server

When it is no longer necessary to retrieve incremental logical logs, the YStream client's close method can be called to close the connection to the YStream server.

```java
client.close();
```
