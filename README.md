# Reactive Relational Database Connectivity Connection Pool Implementation [![Concourse CI](https://ci.spring.io/api/v1/teams/r2dbc/pipelines/r2dbc/jobs/r2dbc-pool/badge)](https://ci.spring.io/teams/r2dbc/pipelines/r2dbc/jobs/r2dbc-pool/) [![Maven Central](https://maven-badges.herokuapp.com/maven-central/io.r2dbc/r2dbc-pool/badge.svg)](https://maven-badges.herokuapp.com/maven-central/io.r2dbc/r2dbc-pool)

This project contains a [R2DBC][r] connection pool using `reactor-pool` for reactive connection pooling.

[r]: https://github.com/r2dbc/r2dbc-spi

## Code of Conduct

This project is governed by the [Spring Code of Conduct](CODE_OF_CONDUCT.adoc). By participating, you are expected to uphold this code of conduct. Please report unacceptable behavior to [spring-code-of-conduct@pivotal.io](mailto:spring-code-of-conduct@pivotal.io).

## Getting Started

Configuration of the `ConnectionPool` can be accomplished in several ways:

**URL Connection Factory Discovery**

```java
ConnectionFactory connectionFactory = ConnectionFactories.get("r2dbc:pool:<my-driver>://<host>:<port>/<database>");

Publisher<? extends Connection> connectionPublisher = connectionFactory.create();
```

**Programmatic Connection Factory Discovery**

```java
ConnectionFactory connectionFactory = ConnectionFactories.get(ConnectionFactoryOptions.builder()
   .option(DRIVER, "pool")
   .option(PROTOCOL, "h2") // driver identifier, PROTOCOL is delegated as DRIVER by the pool.
   .option(HOST, "…")
   .option(PORT, "…") 
   .option(USER, "…")
   .option(PASSWORD, "…")
   .option(DATABASE, "…")
   .build());
```

The delegated `DRIVER` (via `PROTOCOL`) above refers to the r2dbc-driver, such as `h2`, `postgresql`, `mssql`, `mysql`, `spanner`.

```java
Publisher<? extends Connection> connectionPublisher = connectionFactory.create();

// Alternative: Creating a Mono using Project Reactor
Mono<Connection> connectionMono = Mono.from(connectionFactory.create());
```

**Supported ConnectionFactory Discovery Options**

| Option            | Description
| ------            | -----------
| `driver`          | Must be `pool`
| `protocol`        | Driver identifier. The value is propagated by the pool to the `driver` property.
| `acquireRetry`    | Number of retries if the first connection acquiry attempt fails. Defaults to `1`.
| `initialSize`     | Initial pool size. Defaults to `10`.
| `maxSize`         | Maximum pool size. Defaults to `10`.
| `validationDepth` | Validation depth used to validate an R2DBC connection. Defaults to `LOCAL`.
| `validationQuery` | Query that will be executed just before a connection is given to you from the pool to validate that the connection to the database is still alive.

All other properties are driver-specific

**Programmatic Setup**

```java
ConnectionFactory connectionFactory = …;

ConnectionPoolConfiguration configuration = ConnectionPoolConfiguration.builder(connectionFactory)
   .maxIdleTime(Duration.ofMillis(1000))
   .maxSize(20)
   .build();

ConnectionPool pool = new ConnectionPool(configuration);
 

Mono<Connection> connectionMono = pool.create();

// later

Connection connection = …;
Mono<Void> release = connection.close(); // released the connection back to the pool

// application shutdown
pool.dispose();
```

### Maven configuration

Artifacts can be found on [Maven Central](https://search.maven.org/search?q=r2dbc-pool).

```xml
<dependency>
  <groupId>io.r2dbc</groupId>
  <artifactId>r2dbc-pool</artifactId>
  <version>0.8.0.RELEASE</version>
</dependency>
```

If you'd rather like the latest snapshots of the upcoming major version, use our Maven snapshot repository and declare the appropriate dependency version.

```xml
<dependency>
  <groupId>io.r2dbc</groupId>
  <artifactId>r2dbc-pool</artifactId>
  <version>${version}.BUILD-SNAPSHOT</version>
</dependency>

<repository>
  <id>spring-libs-snapshot</id>
  <name>Spring Snapshot Repository</name>
  <url>https://repo.spring.io/libs-snapshot</url>
</repository>
``` 

## Getting Help

Having trouble with R2DBC? We'd love to help!

* Check the [spec documentation](https://r2dbc.io/spec/0.8.0.RELEASE/spec/html/), and [Javadoc](https://r2dbc.io/spec/0.8.0.RELEASE/api/).
* If you are upgrading, check out the [changelog](https://r2dbc.io/spec/0.8.0.RELEASE/CHANGELOG.txt) for "new and noteworthy" features.
* Ask a question - we monitor [stackoverflow.com](https://stackoverflow.com) for questions
  tagged with [`r2dbc`](https://stackoverflow.com/tags/r2dbc). 
  You can also chat with the community on [Gitter](https://gitter.im/r2dbc/r2dbc).
* Report bugs with R2DBC Pool at [github.com/r2dbc/r2dbc-pool/issues](https://github.com/r2dbc/r2dbc-pool/issues).

## Reporting Issues

R2DBC uses GitHub as issue tracking system to record bugs and feature requests. 
If you want to raise an issue, please follow the recommendations below:

* Before you log a bug, please search the [issue tracker](https://github.com/r2dbc/r2dbc-pool/issues) to see if someone has already reported the problem.
* If the issue doesn't already exist, [create a new issue](https://github.com/r2dbc/r2dbc-pool/issues/new).
* Please provide as much information as possible with the issue report, we like to know the version of R2DBC Pool that you are using and JVM version.
* If you need to paste code, or include a stack trace use Markdown ``` escapes before and after your text.
* If possible try to create a test-case or project that replicates the issue. 
Attach a link to your code or a compressed file containing your code.

## Building from Source

You don't need to build from source to use R2DBC Pool (binaries in Maven Central), but if you want to try out the latest and greatest, R2DBC Pool can be easily built with the
[maven wrapper](https://github.com/takari/maven-wrapper). You also need JDK 1.8 and Docker to run integration tests.

```bash
 $ ./mvnw clean install
```

If you want to build with the regular `mvn` command, you will need [Maven v3.5.0 or above](https://maven.apache.org/run-maven/index.html).

_Also see [CONTRIBUTING.adoc](CONTRIBUTING.adoc) if you wish to submit pull requests, and in particular please sign the [Contributor's Agreement](https://cla.pivotal.io/sign/spring) before your first change, however trivial._


## License
This project is released under version 2.0 of the [Apache License][l].

[l]: https://www.apache.org/licenses/LICENSE-2.0
