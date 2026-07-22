## Configuring the Database YStream Server

1. Connect and log in to the database as a DBA user — if it is a primary/standby deployment, log in to the primary database/cluster.

2. Create the database user ystream_user and grant it the YSTREAM_CAPTURE privilege.

3. Enable supplemental logging in one of the following two ways:

  - [Database-level supplemental logging](../SQL Reference Manual/SQL Statements/ALTER DATABASE.md#supplementallogclauses):

    ```sql
    ALTER DATABASE ADD SUPPLEMENTAL LOG TABLE TYPE (HEAP);
    ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
    ```

  - [Table-level supplemental logging](../SQL Reference Manual/SQL Statements/ALTER DATABASE.md#addsupplementalloggingclause):
    
    ```sql
    ALTER TABLE tablename ADD SUPPLEMENTAL LOG DATA PRIMARY KEY COLUMNS;
    ```

4. Create the YStream server, using the current SCN of the database as an example.

    ```sql
    EXEC DBMS_YSTREAM_ADM.CREATE('server_1', 'ystream_user', null);
    ```

5. (Optional) Specify the table names to be parsed, indicating 2 tables as an example.

    If the scope to be parsed is all tables in the database, this operation is not required.

    ```sql
    EXEC DBMS_YSTREAM_ADM.ADD_TABLES('server_1', 'sales.employees,sales.department', null);
    ```

6. Start the YStream server.

    >**Note**:
    >
    > In primary/standby deployment, if you need to run the YStream server on a standby database/cluster, you must log in to the target standby database/cluster as ystream_user before executing the start operation.

    ```sql
    EXEC DBMS_YSTREAM_ADM.START('server_1');
    ```

7. Configure YStream client parameters to connect to and start the YStream server for parsing — the client example is as follows:

## Developing Applications Based on YStream Interface

```java
package ystream0;

// Ystreamexample.java
// Demonstrates the main steps of developing based on YStream

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
  private static final String HOST = "192.168.1.2";   // must specify the node for executing the YStream server start operation
  private static final String PORT = "1688";
  private static final String USER = "ystream_user";
  private static final String PASSWORD = "ystream_password";
  private static final String SERVER_NAME = "server_1";

  public static void main(String[] args) {
    // Parse command line arguments to determine if resuming from a breakpoint
    List<String> argList = Arrays.stream(args).collect(Collectors.toList());
    boolean isRecover = Boolean.parseBoolean(argList.get(0));
    // Get YStream client instance
    YstreamClientBoot<String> client = YstreamClientBoot.getClient();

    // Create YStream client configuration class
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
    // Create a timer thread to clean up used logical logs every 3 seconds
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
      // Create YStream server connection
      client.open(config);
      customDeserializer.setCharset(client.getCharset());
      customDeserializer.setNationalCharset(client.getNationalCharset());
      while (true) {
        // Get incremental logical logs
        String next = client.next();
        if (next != null) {
          // Print incremental logical logs
          System.out.println(next);
        }
      }
    } catch (Exception e) {
      e.printStackTrace();
    } finally {
      timer.cancel();
      task.cancel();
      // Close YStream server connection
      client.close();
    }
  }
}
```

```java
package ystream0;

// CustomDeserializer
// Demonstrates a custom deserializer implementing the Deserializer interface for SQL deserialization

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
