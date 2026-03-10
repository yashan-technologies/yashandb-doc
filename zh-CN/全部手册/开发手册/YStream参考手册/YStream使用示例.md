### 配置数据库YStream服务

1. 以DBA用户连接并登录数据库，若为主备部署环境则需登录主库/主集群。

2. 创建数据库用户ystream_user，并赋予YSTREAM_CAPTURE权限。

3. 开启附加日志，操作方式有以下两种：

  - [库级附加日志](../SQL参考手册/SQL语句/ALTER DATABASE.html#supplementallogclauses)：

    ```sql
    ALTER DATABASE ADD SUPPLEMENTAL LOG TABLE TYPE (HEAP);
    ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
    ```

  - [表级附加日志](../SQL参考手册/SQL语句/ALTER DATABASE.html#addsupplementalloggingclause)：
    
    ```sql
    ALTER TABLE tablename ADD SUPPLEMENTAL LOG DATA PRIMARY KEY COLUMNS;
    ```

4. 创建YStream服务，以使用数据库当前SCN为例。

    ```sql
    EXEC DBMS_YSTREAM_ADM.CREATE('server_1', 'ystream_user', null);
    ```

5. 指定要解析的表名，以指定2张表为例。

    ```sql
    EXEC DBMS_YSTREAM_ADM.ADD_TABLES('server_1', 'sales.employees,sales.department', null);
    ```

6. 启动YStream服务。

    >**Note**:
    >
    > 在主备部署环境中，如需在备库/备集群上运行YStream服务则需以ystream_user用户登录目标备库或备集群再执行启动操作。

    ```sql
    EXEC DBMS_YSTREAM_ADM.START('server_1');
    ```

7. 配置YStream客户端参数，连接YStream服务并启动解析，客户端示例如下文所述。

### 基于YStream接口开发应用程序

```java
package ystream0;

// Ystreamexample.java
// 演示基于YStream开发的主要步骤

import com.sics.ystream.YstreamClientBoot;
import com.sics.ystream.conf.StartMode;
import com.sics.ystream.conf.YstreamConfig;
import com.sics.ystream.result.LogPosition;
import com.sics.ystream.result.Position;
import com.sics.ystream.result.SystemChangeNumber;
import java.util.Arrays;
import java.util.List;
import java.util.Timer;
import java.util.TimerTask;
import java.util.stream.Collectors;

public class Ystreamexample {
  private static final String HOST = "192.168.1.2";   // 需指定为执行YStream服务启动操作的节点
  private static final String PORT = "1688";
  private static final String USER = "ystream_user";
  private static final String PASSWORD = "ystream_password";
  private static final String SERVER_NAME = "server_1";

  public static void main(String[] args) {
    // 解析命令行参数，判断是否断点续传
    List<String> argList = Arrays.stream(args).collect(Collectors.toList());
    boolean isRecover = Boolean.parseBoolean(argList.get(0));
    // 获取YStream客户端实例
    YstreamClientBoot<String> client = YstreamClientBoot.getClient();

    // 创建YStream客户端配置类
    CustomDeserializer customDeserializer = new CustomDeserializer();
    YstreamConfig.Builder<String> configBuilder = YstreamConfig.<String>builder();
    configBuilder
        .setHost(HOST)
        .setPort(PORT)
        .setUser(USER)
        .setPassword(PASSWORD)
        .setServerName(SERVER_NAME)
        .setStartMode(isRecover ? StartMode.RECOVER : StartMode.START)
        .setDeserializer(customDeserializer);
    if (isRecover) {
      Position recoverPosition =
          new Position(
              new SystemChangeNumber(Long.parseLong(argList.get(1))),
              new LogPosition(
                  Byte.parseByte(argList.get(2)),
                  Long.parseLong(argList.get(3)),
                  Integer.parseInt(argList.get(4)),
                  Integer.parseInt(argList.get(5))));
      configBuilder.setRecoverPosition(recoverPosition);
    }
    YstreamConfig<String> config = configBuilder.build();
    // 创建定时线程，每3秒清理一次已使用的逻辑日志
    Timer timer = new Timer();
    TimerTask task =
        new TimerTask() {
          @Override
          public void run() {
            Position currentPosition = customDeserializer.getCurrentPosition();
            if (currentPosition != null) {
              client.setAppliedPosition(currentPosition);
            }
          }
        };
    long delay = 0;
    long period = 3000L;
    timer.scheduleAtFixedRate(task, delay, period);
    try {
      // 创建YStream服务端连接
      client.open(config);
      customDeserializer.setCharset(client.getCharset());
      customDeserializer.setNationalCharset(client.getNationalCharset());
      while (true) {
        // 获取增量逻辑日志
        String next = client.next();
        if (next != null) {
          // 打印增量逻辑日志
          System.out.println(next);
        }
      }
    } catch (Exception e) {
      e.printStackTrace();
    } finally {
      timer.cancel();
      task.cancel();
      // 关闭YStream服务端连接
      client.close();
    }
  }
}
```

```java
package ystream0;

// CustomDeserializer
// 演示实现Deserializer接口的自定义反序列化器反序列化为SQL

import com.sics.ystream.deserializer.Deserializer;
import com.sics.ystream.exception.YstreamException;
import com.sics.ystream.exception.YstreamIoException;
import com.sics.ystream.exception.YstreamSqlException;
import com.sics.ystream.metadata.Column;
import com.sics.ystream.metadata.ColumnType;
import com.sics.ystream.metadata.TableMetadata;
import com.sics.ystream.result.Position;
import com.sics.ystream.result.YstreamChunk;
import com.sics.ystream.result.YstreamColumn;
import com.sics.ystream.result.YstreamColumns;
import com.sics.ystream.result.YstreamDdl;
import com.sics.ystream.result.YstreamDml;
import com.sics.ystream.result.YstreamLcrInterface;
import com.sics.ystream.result.YstreamXactInterface;
import com.sics.ystream.util.ChunkUtil;
import com.sics.ystream.util.YstreamUtil;
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.nio.charset.Charset;
import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import java.util.TreeMap;
import java.util.stream.Collectors;

public class CustomDeserializer implements Deserializer<String> {
  private Charset charset;
  private Charset nationalCharset;
  private YstreamDml lcrCache = null;
  private List<byte[]> chunkList = new ArrayList<>();
  private Map<Integer, String> map = new TreeMap<>();
  private int chunkDataCount;

   private Position currentPosition;

   public void setCharset(Charset charset) {
      this.charset = charset;
   }

   public void setNationalCharset(Charset nationalCharset) {
      this.nationalCharset = nationalCharset;
   }

   public Position getCurrentPosition() {
      return currentPosition;
   }

  @Override
  public String deserialize(YstreamLcrInterface lcr, TableMetadata tableMetadata)
      throws YstreamException {
    switch (lcr.getLcrType()) {
      case YSTREAM_DDL:
        YstreamDdl ddl = (YstreamDdl) lcr;
        currentPosition = ddl.getPosition();
        return ddl.getDdlText() + ";";
      case YSTREAM_DML:
        final YstreamDml dml = (YstreamDml) lcr;
        currentPosition = dml.getPosition();
        if (dml.hasChunkData()) {
          lcrCache = dml;
          chunkDataCount = lcrCache.getChunkDataCount();
          assert chunkDataCount > 0;
          return "";
        } else {
          return dml.getStatement(false) + ";";
        }
      case YSTREAM_CHUNK:
        YstreamChunk chunk = (YstreamChunk) lcr;
        currentPosition = chunk.getPosition();
        if (chunkDataCount > 0) {
          if (!chunk.isEnd()) {
            chunkList.add(chunk.getBytes());
          } else {
            try {
              map.put(
                  chunk.getColumn().getColumnId(),
                  concat(chunkList, chunk.getColumn(), charset, nationalCharset));
            } catch (IOException e) {
              throw new YstreamIoException(e);
            }
            chunkDataCount--;
          }
        }
        String sql = lcrCache.getStatement(true);
        YstreamColumns newValues = lcrCache.getNewValues();
        for (YstreamColumn column : newValues.getColumns()) {
          if (!column.isOutRow()) {
            map.put(column.getColumn().getColumnId(), getDataString(column));
          }
        }
        List<String> lists = new ArrayList<>(map.values());
        lists.addAll(
            lcrCache.getOldValues().getColumns().stream()
                .map(
                    o -> {
                      try {
                        return getDataString(o);
                      } catch (YstreamSqlException e) {
                        throw new RuntimeException(e);
                      }
                    })
                .collect(Collectors.toList()));
        int offset = 0;
        for (String s : lists) {
          for (int j = offset; j < sql.length(); j++) {
            if (sql.charAt(j) == '?') {
              sql = sql.replaceFirst("/?", s);
              offset += s.length();
            }
            offset++;
          }
        }
        return sql + "\n";
      case YSTREAM_XACT:
        final YstreamXactInterface xact = (YstreamXactInterface) lcr;
        switch (xact.getXactType()) {
          case BEGIN:
            return "begin;";
          case COMMIT:
            currentPosition = xact.getPosition();
            return "commit;";
          default:
            throw new UnsupportedOperationException(String.format("Not support data, %s.", xact));
        }
      case YSTREAM_METADATA:
        return "";
      default:
        throw new UnsupportedOperationException(
            String.format("lcr type error: %s.", lcr.getLcrType().toString()));
    }
  }

  private static String getDataString(YstreamColumn column) throws YstreamSqlException {
    int dataType = column.getColumn().getDataType();
    if (ColumnType.isCharacterType(dataType) || ColumnType.isBlobType(dataType) || ColumnType.isClobType(dataType)) {
      return YstreamUtil.singleQuotation(column.getString());
    }
    return column.getString();
  }

  private static String concat(
      List<byte[]> chunkList, Column column, Charset charset, Charset nCharset)
      throws IOException, YstreamSqlException {
    ByteArrayOutputStream stream = new ByteArrayOutputStream();
    for (byte[] bytes : chunkList) {
      stream.write(bytes);
    }
    return YstreamUtil.singleQuotation(ChunkUtil.parseString(stream.toByteArray(), column, charset, nCharset));
  }
}
```
