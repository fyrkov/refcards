### Spring cloud stream 

Spring cloud stream is a messaging framework that can be backed by:
- Apache Kafka
- RabbitMQ
- others

![example_schema](spring-cloud-files/spring-cloud-stream-1.gif)

**Source** - publishes a message to channel
**Channel** - abstraction over a queue, holds messages. Bind to a queue in configs
**Binder** - talks to a specific messaging platform, wrapping specific libraries and APIs
**Sink** - listens to a channle for incoming messages and de-serializes them.

### Usage

1. Add a dependencies, e.g.
```
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-stream-binder-kafka</artifactId>
</dependency>
```
2. Use `@EnableBinding(Source.class)` on configuration class to bind an application to a message broker

3. `Source` interface exposes API to send messages, e.g.
```
source.output().send(new Message(payload));
```
Note: `output` and `output` are predefined default channels. Custom channels can be created.

4. Configure the service to use a messaging system
```
  spring.cloud.stream.bindings:
    output: # default channel
      destination: someQueue # name of the message queue
      content-type: application/json
    kafka: # tells to use kafka as messaging system
      binder:
        zknodes: localhost
        brokers: localhost
```

5. Use `@EnableBinding(Sink.class)` on configuration class for consumer service.

6. Put `@StreamListener(Sink.INPUT)` or `@StreamListener("customChannel")` on a method for messages processing.

7. Configure counsumer with to use a messaging system

:exclamation: A message is guaranteed to be consumed only once within one group of consumer instanes, but the same message can be duplicated to another group, see `spring.stream.bindings.input.group`.