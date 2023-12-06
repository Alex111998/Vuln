When I test the latest version(3.3.1) of mariadb-java-client by CIFuzz，a OOM security issue was found, it caused when put a big number in ParameterList, may cause denial of service issues in applications via the follow code:

pom
```
<dependency>
   <groupId>org.mariadb.jdbc</groupId>
   <artifactId>mariadb-java-client</artifactId>
   <version>3.3.1</version>
</dependency>
```
code1
```
import org.mariadb.jdbc.util.ParameterList;

public class Mariadb_OOM {

    public static void main(String[] args) {
        try {
            ParameterList parameterList = new ParameterList();
            parameterList.set(1832742252, null);
        } catch (Exception e) {
        }
    }
}
```
![image](https://github.com/Alex111998/Vuln/assets/127834723/e716ba42-3eda-4601-8c0e-172604c20f50)

code2
```
import org.mariadb.jdbc.client.impl.PrepareCache;
import org.mariadb.jdbc.message.server.CachedPrepareResultPacket;

public class Mariadb_OOM {

    public static void main(String[] args) {
        try {
            PrepareCache prepareCache = new PrepareCache(1832742252, null);
            CachedPrepareResultPacket result = prepareCache.put("", null);
        } catch (Exception e) {
        }
    }
}
```

