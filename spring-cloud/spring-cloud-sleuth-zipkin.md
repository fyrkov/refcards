### Spring Cloud distributed tracing with Sleuth and Zipkin

#### Spring Cloud Sleuth
Sleuth is a project that adds distributed tracing features like:
- creates and injects `traceId` header into sesrvice calls if it does not exist
- manages the propagation of the headers to outbound calls
- adds correlation ids to application logs
- publish tracing info to Zipkin (optional)

:exclamation: by default Sleuth does not expose `traceId` headers in responses.

Sleuth maps each of the service call to the concept of *span* - a part of the overall transaction, which one particular service carries out.

:exclamation: Sleuth does not propagate `traceId` if services communicate with messaging, i.e. consumer of a message will have a new `traceId`.

#### Usage

1. Add a dependency
```
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
```

#### Zipkin
Zipkin is a data vizualization tool that shows transactions flow across multiple services.\
It consists of a client library and a server.\
Sleuth atomatically integrates with Zipkin.

By default Zipkin uses in-memory data store but can be configured to use MySQL, Elasticsearch or Cassandra.\
:exclamation: In-memory store holds limited amount of data and loses all data on service restart.

#### Usage

1. Add a dependency
```
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-sleuth-zipkin</artifactId>
</dependency>
```

2. In each service define a URL to communicate with zipkin in `appication.yml`
```
spring.zipkin.baseUrl: http://host:port
```

3. In each service define how many transactions will be recorded
```
spring.sleuth.sampler.percentage: 0.01
```

4. Set up and configure Zipkin server with 
```
<dependency>
  <groupId>io.zipkin.java</groupId>
  <artifactId>zipkin-server</artifactId>
</dependency>
<dependency>
  <groupId>io.zipkin.java</groupId>
  <artifactId>zipkin-autoconfigure-ui</artifactId>
</dependency>
```
and `@EnableZipkinServer`

5. It is possible to add a span to 3rd party services calls, like Postgres or Redis, which can not be injected directly with Zipkin library.
```
@Autowired Tracer tracer;
...
var newSpan = tracer.createSpan("...");
...
tracer.close(newSpan)
```