# Geode Logback

This project contains a simple maven `pom.xml` which excludes the log4j-core dependency from geode-core while also adding logback-classic and log4j-to-slf4j. The JUnit test `GeodeApplicationIntegrationTest` then demonstrates the creation of a Geode Cache with all Geode logging directed to Logback instead of log4j-core.

As of **Apache Geode 1.9.0**, all of the code that uses log4j-core is optional such that if you remove log4j-core from your classpath, none of the code requiring it will be used. Since log4j-core is a transitive dependency of geode-core, it will be pulled in automatically unless you explicitly exclude it like this project does.

# What is log4j-core?

Log4j-core is a backend similar to Logback, and log4j-api is a frontend similar to slf4j. You can wire up log4j-api to use logback as its backend and you can wire up slf4j to use log4j-core as its backend.

These various combinations do have performance implications and should be thoroughly tested and benchmarked.

# Why does Geode use log4j-core

Apache Geode is the open-source inheritor of *GemFire* which contained its own proprietary logging system including API and backend. That logging system was reimplemented using log4j-core appenders to maintain the existing behavior for backwards compatibility. The following Geode configuration properties are used to configure this logging system:
* `log-disk-space-limit`
* `log-file`
* `log-file-size-limit`
* `log-level`

The **Geode Alerting** system is also implemented using log4j-core appenders because Alerts are generated for log statements that match a specified log level. The default threshold for Alerts is `warning`.

Excluding log4j-core from the classpath will completely disable the Geode configuration properties for logging, GFSH commands related to altering log level or viewing log files, and the Geode Alerting system.

# Geode Statistics

The **Geode Statistics** archiving system is designed to mimic similar file rolling and disk space usage limiting behavior as **Geode Logging**, but it is *not* dependent on log4j-core.

# org.apache.geode.internal.logging.ProviderAgent

This project contains a partially stubbed out LogbackAgent class which implements the `org.apache.geode.internal.logging.ProviderAgent` service loader interface from Apache Geode.

Implementing a class such as LogbackAgent is not necessary to get Geode to log to Logback. However, implementing it would be necessary to fully support the Geode logging configuration properties and GFSH commands related to altering log level or viewing log files.

Note: `org.apache.geode.internal.logging.ProviderAgent` is currently an internal SPI and may change further before becoming fully supported.

# org.apache.geode.internal.alerting.AlertingProvider

To support the Geode Alerting system, the developer would also need to implement the `org.apache.geode.internal.alerting.AlertingProvider` service loader interface. Without a Logback implementation, Alerting is disabled while using Logback.

Note: `org.apache.geode.internal.alerting.AlertingProvider` is currently an internal SPI and may change further before becoming fully supported.

# Possible future changes to Geode

Moving all geode-core code that uses log4j-core into a new `geode-log4j` submodule would greatly facilitate setting up a project like this because you could simply include or exclude the geode-log4j dependency.

Similarly, Geode could provide full support for Logback in a new `geode-logback` submodule.

Completely removing the usage of a logging backend from Apache Geode would remove features that are required for backwards compatibility. This would have to coincide with a major release, such as Geode 2.0.
