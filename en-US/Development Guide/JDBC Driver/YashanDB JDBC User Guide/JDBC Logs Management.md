The YashanDB JDBC driver records logs at runtime, divided into five levels: error, warn, info, debug, and trace.

The YashanDB JDBC driver's logging function supports SLF4J and JCL (Apache Commons Logging) facade technologies, and supports mainstream logging frameworks such as Log4j 2, Logback, java.util.logging (JUL), etc. It can also redirect Log4j 1 and others to SLF4J through bridges and then output them.

To quickly identify performance issues occurring in business, the YashanDB JDBC driver adds log information at the start or end and the exit points of all driver interfaces of operations such as obtaining connections, closing connections, SQL pre-compilation, binding execution, direct execution, and fetching results; these logs are at trace level. Opening trace will have a certain impact on performance, please select whether to open it according to the actual problem location.

Under normal circumstances, users do not need to perform additional log configuration; the system will print the JDBC driver's logs along with the application's business logs according to the logging level configured by the application.

### Log Content

**Entry Log**

Format: >> ENTER: {}.{}({}), timestamp:{}, {}:{}.

Where {} is a placeholder, representing the class name, method name, parameters, timestamp, class name, and line number, respectively.

**Obtain Connection**

Start: `connect start, timestamp:{}, ip/port:{}, serverType:{}`.

End: `connect end, timestamp:{}, session id:{}, connectVersion:{}`.

**Close Connection**

Start: `connect close start, timestamp:{}, session id:{}`.

End: `connect close end, timestamp:{}, session id:{}`.

**Pre-compile**

Start: `prepare start, timestamp:{}, session id:{}, sql:{}`.

End: `prepare end, timestamp:{}, session id:{}, statement id:{}`.

**Bind Execute**

Start: `prepareStatement execute start, timestamp:{}, session id:{}, Statement id:{}`.

End: `prepareStatement execute end, timestamp:{}, session id:{}, Statement id:{}`.

**Direct Execute**

Start: `statement execute start, timestamp:{}, Statement id:{}, sql:{}`.

End: `statement execute end, timestamp:{}, session id:{}, Statement id:{}`.

**fetch**

Start: `fetchMore start, timestamp:{}, session id:{}, Statement id:{}`.

End: `fetchMore end, timestamp:{}, session id:{}, Statement id:{}`.

### Log Level and Path Independent Configuration

The interaction information's log level is trace, and to print such logs, one only needs to adjust the application's log level to trace. However, this configuration will cause all trace-level logs of the application to be printed, which may lead to excessive log outputs.

To avoid printing excessive irrelevant log information, it is advisable to independently configure the log level and path for the driver package (com.yashandb), setting the log level of the com.yashandb package to trace or separating driver logs and business logs into different paths.

Mainstream frameworks such as log4j, log4j2, logback, and JUL support setting log levels and paths by package. The following example shows the logging-config.xml configuration for Spring Boot's default logback logging framework. Similar configurations apply to other logging frameworks.

> **Note**:
>
> In the native JDK's JUL logging framework, log levels range from high to low as SEVERE, WARNING, INFO, CONFIG, FINE, FINER, FINEST, etc. The trace level in other logging frameworks corresponds to the FINEST level in JUL.

```xml
<configuration>
    <contextName>jdbc-mybatis</contextName>
    <property name = "dir" value = "log"/>

    <appender name="consoleLog" class="ch.qos.logback.core.ConsoleAppender">
        <!--Display format layout-->
        <layout class="ch.qos.logback.classic.PatternLayout">
            <pattern>
                <pattern>%d{HH:mm:ss.SSS} %contextName [%thread] %-5level %logger{36} - %msg%n</pattern>
            </pattern>
        </layout>

    </appender>

    <appender name="fileLog" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <File>${dir}/project_log.log</File>
        <!--Rolling policy, classified by time-->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--File path, defines log slicing method—archiving each day's logs to a file to prevent logs from filling up the entire disk space-->
            <FileNamePattern>${dir}/project_log.%d{yyyy-MM-dd}.log</FileNamePattern>
            <!--Only keep logs for the last 90 days-->
            <maxHistory>90</maxHistory>

        </rollingPolicy>
        <!--Log output encoding format-->
        <encoder>
            <charset>UTF-8</charset>
            <pattern>%d [%thread] %-5level %logger{36} %line - %msg%n</pattern>
        </encoder>
    </appender>

    <appender name="fileLog1" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <File>${dir}/driver_log.log</File>
        <!--Rolling policy, classified by time-->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--File path, defines log slicing method—archiving each day's logs to a file to prevent logs from filling up the entire disk space-->
            <FileNamePattern>${dir}/driver_log.%d{yyyy-MM-dd}.log</FileNamePattern>
            <!--Only keep logs for the last 90 days-->
            <maxHistory>90</maxHistory>

        </rollingPolicy>
        <!--Log output encoding format-->
        <encoder>
            <charset>UTF-8</charset>
            <pattern>%d [%thread] %-5level %logger{36} %line - %msg%n</pattern>
        </encoder>
    </appender>
    
    <!-- Set the log level for the driver independently to TRACE -->
    <logger name="com.yashandb" level="TRACE" additivity="false">
        <!-- Configure the driver log appender to fileLog1 defined above, i.e., ${dir}/driver_log.log -->
        <appender-ref ref="fileLog1"/>
    </logger>

    <!-- Set the log level of the DAO layer to DEBUG -->
    <logger name="com.example.dao" level="DEBUG" additivity="false">
        <appender-ref ref="fileLog"/>
    </logger>
    
    <root level="INFO">
        <!--Appenders will be added to this logger-->
        <appender-ref ref="consoleLog1"/>
        <appender-ref ref="fileLog"/>
    </root>

</configuration>
```

