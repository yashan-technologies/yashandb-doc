YashanDB JDBC驱动在运行时会记录日志，日志分为error、warn、info、debug和trace五种级别。

YashanDB JDBC驱动的日志功能支持SLF4J和JCL（Apache Commons Logging）两种门面技术，支持目前主流的日志框架，例如Log4j 2、Logback、java.util.logging (JUL)等，也可以通过桥接器把Log4j 1等通过桥接的方式重定向到SLF4J然后输出。

为了快速定界业务中出现的问题，YashanDB JDBC驱动在获取连接、关闭连接、sql预编译、绑定执行、直接执行、fetch结果集等操作的开始或结束以及所有的驱动接口出口处都分别加入了日志信息，这些日志的级别为trace。打开trace会对性能产生一定影响，请根据实际情况问题定位时选择是否打开。

正常情况下用户无需额外进行日志配置，系统会根据应用所配置的日志级别将JDBC驱动的日志和应用的业务日志一起打印。

### 日志内容

**入口日志**

格式为：>> ENTER：{}.{}({})，timestamp：{}，{}：{}。

其中{}为占位符，分别表示类名、方法名、参数、时间戳、类名、行名。

**获取连接**

开始：`connect start,timestamp:{},ip/port:{},serverType:{}`。

结束：`connect end,timestamp:{},session id:{},connectVersion:{}`。

**关闭连接**

开始：`connect close start,timestamp:{},session id:{}`。

结束：`connect close end,timestamp:{},session id:{}`。

**预编译**

开始：`prepare start,timestamp:{},session id:{}, sql:{}`。

结束：`prepare end,timestamp:{},session id:{},statement id:{}`。

**绑定执行**

开始：`prepareStatement execute start,timestamp:{},session id:{}, Statement id:{}`。

结束：`prepareStatement execute end,timestamp:{},session id:{}, Statement id:{}`。

**直接执行**

开始：`statement execute start,timestamp:{}, Statement id:{},sql:{}`。

结束：`statement execute end,timestamp:{},session id:{}, Statement id:{}`。

**fetch**

开始：`fetchMore start,timestamp:{},session id:{}, Statement id:{}`。

结束：`fetchMore end,timestamp:{},session id:{}, Statement id:{}`。

### 日志级别与路径单独配置

交互信息的日志级别为trace，如需打印此类日志只需将应用的日志级别调整成trace级别即可，但此配置会让应用的所有trace级别日志全部被打印，容易引起日志过多等问题。

为避免打印过多无关日志信息，建议单独配置驱动包（com.yashandb）的日志级别和路径，将com.yashandb包的日志级别设置为trace，或将驱动日志和业务日志分开打印在不同路径下。

log4j、log4j2、logback、JUL等主流框架均支持分包设置日志级别和日志路径，以下示例为spring-boot默认的logback日志框架的logging-config.xml写法，其他日志框架配置类似。

> **Note**:
>
> 在JDK原生的JUL日志框架中，日志级别从高到低分别为SEVERE，WARNING，INFO，CONFIG，FINE，FINER，FINEST等，其它日志框架的trace级别对应于JUL的FINEST级别。

```xml
<configuration>
    <contextName>jdbc-mybatis</contextName>
    <property name = "dir" value = "log"/>

    <appender name="consoleLog" class="ch.qos.logback.core.ConsoleAppender">
        <!--展示格式 layout-->
        <layout class="ch.qos.logback.classic.PatternLayout">
            <pattern>
                <pattern>%d{HH:mm:ss.SSS} %contextName [%thread] %-5level %logger{36} - %msg%n</pattern>
            </pattern>
        </layout>

    </appender>

    <appender name="fileLog" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <File>${dir}/project_log.log</File>
        <!--滚动策略，按照时间滚动 TimeBasedRollingPolicy-->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--文件路径,定义了日志的切分方式——把每一天的日志归档到一个文件中,以防止日志填满整个磁盘空间-->
            <FileNamePattern>${dir}/project_log.%d{yyyy-MM-dd}.log</FileNamePattern>
            <!--只保留最近90天的日志-->
            <maxHistory>90</maxHistory>

        </rollingPolicy>
        <!--日志输出编码格式化-->
        <encoder>
            <charset>UTF-8</charset>
            <pattern>%d [%thread] %-5level %logger{36} %line - %msg%n</pattern>
        </encoder>
    </appender>

    <appender name="fileLog1" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <File>${dir}/driver_log.log</File>
        <!--滚动策略，按照时间滚动 TimeBasedRollingPolicy-->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--文件路径,定义了日志的切分方式——把每一天的日志归档到一个文件中,以防止日志填满整个磁盘空间-->
            <FileNamePattern>${dir}/driver_log.%d{yyyy-MM-dd}.log</FileNamePattern>
            <!--只保留最近90天的日志-->
            <maxHistory>90</maxHistory>

        </rollingPolicy>
        <!--日志输出编码格式化-->
        <encoder>
            <charset>UTF-8</charset>
            <pattern>%d [%thread] %-5level %logger{36} %line - %msg%n</pattern>
        </encoder>
    </appender>
    
    <!-- 单独设置驱动的日志级别为TRACE -->
    <logger name="com.yashandb" level="TRACE" additivity="false">
        <!-- 配置驱动的日志appender为上面定义的fileLog1，即${dir}/driver_log.log -->
        <appender-ref ref="fileLog1"/>
    </logger>

    <!-- 设置dao层的日志级别为DEBUG -->
    <logger name="com.example.dao" level="DEBUG" additivity="false">
        <appender-ref ref="fileLog"/>
    </logger>
    
    <root level="INFO">
        <!--appender将会添加到这个loger-->
        <appender-ref ref="consoleLog1"/>
        <appender-ref ref="fileLog"/>
    </root>

</configuration>
```

