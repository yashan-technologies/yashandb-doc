基于FastLoader接口开发应用程序。

```java
package imp;

// 演示基于FastLoader开发的主要步骤

import com.yasdb.FastLoaderFactory;
import com.yasdb.FastLoader;
import com.yasdb.LoaderProgress;
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.Properties;
import java.util.concurrent.TimeUnit;

public class Ystreamexample {
  private static final String ipPort = "127.0.0.1:1688";
  private static final String user = "db_user";
  private static final String password = "db_password";

  public static void main(String[] args) {
    // 创建fastLoader
    Properties info = new Properties();
    info.setProperty("user", user);
    info.setProperty("password", password);
    FastLoader fastLoader = FastLoaderFactory.generateFastLoader("BASIC", ipPort, info);

    // 构建导入的数据
    List data = new LinkedList<>();
    for (int i = 0; i < 10000; i++) {
      List<Object> line = new LinkedList<>();
      int col1 = i;
      String col2 = "This is col2 data";
      String col3 = "This is col3 data";
      line.add(col1);
      line.add(col2);
      line.add(col3);
      data.add(line);
    }

    // 设置属性，开始执行
    fastLoader.setCommitCount(0);
    fastLoader.prepare("table_fast_loader");
    fastLoader.setSendCountAtOnce(10000);
    fastLoader.setReaderCount(2);
    fastLoader.setSenderCount(10);
    fastLoader.execute();

    //多次put数据
    boolean isPutSuccess = fastLoader.putData(data);

    //结束传入数据，获取执行进度
    fastLoader.finish();
    LoaderProgress progress = fastLoader.getProgress();

    while (progress.getPercentage() < 100) {
      try {
          Thread.sleep(500);
      } catch (InterruptedException e) {
      }
    }
    //执行完成获取执行结果
    int errorCount = progress.getErrorCount();
    if (errorCount > 0) {
      StringBuffer errorMsg = progress.getErrorMsg();
    }

    //关闭fastLoader
    fastLoader.close(10, TimeUnit.SECONDS);
  }
}
```