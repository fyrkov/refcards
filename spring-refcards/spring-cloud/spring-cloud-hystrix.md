### Spring cloud resiliency with Netflix Hystrix

There are 4 client-side resiliency patterns
- Client-side load balancing. It caches microservices endpoint retrieved during service discovery.
- Circuit breaker. It ensures that service client does not repeatedly call a failing sevice.
- Fallbacks. Provide alternative reponse if a call fails.
- Bulkheads. Segregates service calls on a client to ensure that a failing service does not occupy all resources on the client.

#### Client-side load balancing
This is what *Netflix Ribbon* libraries do. It does not only cache service locations retrieved from discovery agent (e.g. *Eureka*) but can also detect if a certain service instance is experiencing problems and prevent any future calls to this instance.

#### Circuit breaker
Circuit breaker wraps the call, monitors it's execution time and kills the connection if it takes too long.\
In addition, if enough calls fail it will prevent any future calls to the failing remote service for some time.\
It's also beneficial for the failing remote service because now it has some time to recover.

#### Fallback
Provides an alternative result if a call fails instead of generating an exception.\
It can be for example queueing up user's request for the future processing.

#### Bulkhead
Isolates calls to remote resoureces into separate thread pools and thus reduce the risk that one slow request will take down the whole app.\
Every remote service is assigned to it's own thread pool. 

Netflix Hystrix is a circuit breaker lib.

:exclamation: Hystrix is no longer developed since 2018.

### Usage

1. Add a dependency
```
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-hystrix</artifactId>
</dependency>
```

2. Use `@EnableCircuitBreaker` on configuration class.

3. Use `@HystrixCommand` on a method. Spring will wrap this method calls with a proxy and manage all calls in a dedicated thread pool.\
It makes sense putting `@HystrixCommand` on DB calls or remote service calls.\
All calls that last longer than **1000 ms** will be interrupted and
`HystrixRuntimeException` will be thrown.

:exclamation: By default all calls happen in the same thread pool, i. e. bulkheads are not set up.\
:exclamation: Hystrix does not propagate main thread's context to threads managed by a `@HystrixCommand`, i.e. `ThreadLocal`s will be lost.

`@HystrixCommand` can be configured with:
- timeout interval
- fallback method name
- dedicated thread pool

