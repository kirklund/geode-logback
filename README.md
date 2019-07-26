As of Apache Geode 1.9.0, all of the code that uses log4j-core is optional such that if you remove log4j-core from your classpath, none of the code requiring it will be used. Without log4j-core in the classpath, the log4j2.xml will also be ignored. Since log4j-core is a transitive dependency of geode-core, it will be pulled in automatically unless you explicitly exclude it.

Note that log4j-core is a backend similar to logback and log4j-api is a frontend similar to slf4j. You can wire up log4j-api to use logback as its backend and you can wire up slf4j to use log4j-core as its backend.

I have a sample repo called geode-logback in which I write a simple application in GeodeApplicationIntegrationTest. It just creates a Geode Cache.

The dependencies are setup using maven with this pom.xml exclude log4j-core while adding logback-classic and log4j-to-slf4j:

        <dependency>
            <groupId>org.apache.geode</groupId>
            <artifactId>geode-core</artifactId>
            <version>1.9.0-SNAPSHOT</version>
            <exclusions>
                <exclusion>
                    <groupId>org.apache.logging.log4j</groupId>
                    <artifactId>log4j-core</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.2.3</version>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-to-slf4j</artifactId>
            <version>2.11.1</version>
        </dependency>
We are discussing the possibility of moving all geode-core code that uses log4j-core to a new geode-log4j submodule which would greatly aid in excluding log4j-core in future releases.

Completely removing log4j-core usage from Apache Geode would remove some old features that are currently considered to be required for backwards compatibility. So that removal would likely have to coincide with the release of a major release (2.0).

