### Spring cloud routing with Netflix Zuul

Usually clients do not call services directly.\
Instead, all calls are routed through a service gateway\
which also can represent a single policies enforcement point, e.g.
- security
- injecting traceId headers for distributed logging

:exclamation: because gateways are naturally a bottleneck of the whole application they should be stateless to allow scalability and relatively fast (no slow DB calls).

Netflix Zuul is a reverse proxy that can perform 2 types of tasks:
- mapping routes for requests forwarding
- applying filters on incoming requests and outgoing reponses

### Usage

1. Add a dependency
```
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-zuul</artifactId>
</dependency>
```

2. Use `@EnableZuulProxy` on configuration class to use OOB Zuul functional with Eureka integration or `@EnableZuulServer` to built a custom gateway.

3. By default *Zuul* enables *Eureka* for discovery and *Ribbon* for load balancing.\
Set up Eureka integration in `application.yml`
```
eureka:
  instance:
    preferIpAddress: true # to register IPs instead of DNS server names
  client:
    registerWithEureka: true # to register with Eureka
    fetchRegistry: true # to store local copy of registry. Will be periodically updated
    serviceUrl:
        defaultZone: http://localhost:8761/eureka/ # List of Eureka hosts
```
4. Zuul can perform automatic routing by following the rule:\
`http://{zuul_host}:{zuul_port}/{serviceId}/v1/{path}`

5. Zuul allow manual routing by configuring routes in `application.yml`:
```
zuul:
  routes:
    very-long-service-id: /short-path/**
```
6. All routes can be listed with\
`GET http://{zuul_host}:{zuul_port}/routes`

7. Automatic routing can be disabled with 

```
zuul:
  ignored-services: 'service1, service2'
```
8. Zuul allow manual static routing to services that are not managed by Eureka.\
This is configured in `application.yml`:

```
zuul:
  routes:
    service1:
      path: /service1/**
      url: http://service-1-static-host:8081
```
This approach limits the number of static hosts to one.\
However it is possible to define several static hosts by disabling Eureka support in Ribbon and providing the list of hosts:
```
zuul:
  routes:
    service1:
      path: /service1/**
      serviceId: service1
ribbon:
  eureka:
    enabled: false
service1:
  ribbon:
    # do the "manual discovery"
    listOfServers: http://static-host:8081, http://static-host:8082
```
:exclamation: disabling Eureka support in Ribbon will make Zuul call Eureka every time instead of asking Ribbon, which is capable of caching services locations. 

9. Routing to non-JVM services is possible with 
   - Providing static urls
   - Spring Cloud Sidecar instance that register service with Eureka

10. Zuul can refresh it's routing configuration by serving 
`POST /refresh` endpoint

11. If *Hystrix* is enabled for Zuul, it will short-circuit all requests longer than 1000 ms (default setting).\
However it is possible to adjust the timeout per individual service or for all services with 
```
hystrix.command.default.execution.isolation.thread.timeoutInMilliseconds: 1500
hystrix.command.{serviceId}.execution.isolation.thread.timeoutInMilliseconds: 2500
```
12. There is another timeout to consider - a *Ribbon* timeout. It's default is 5000 ms.\
If it is not enough, it can be adjusted with
```
serviceId.ribbon.ReadTimeout: 7500
```

#### Zuul provides 3 types of filters:
1. Pre-filter is invoked before Zuul will call the target destination. Usually used for authentication and authorization tasks, checking required headers, setting traceId.
```
public class MyFilter extends ZuulFilter {

  public static final FILTER_ORDER = 1; // defines filters execution order

  @Override
  public int filterType() {
    return FilterUtils.PRE_FILTER_TYPE;
  }
  
  @Override
  public int filterOrder() {
    return FILTER_ORDER;
  }

  @Override
  public boolean shouldFilter() {
    // define conditions to enable filter
  }

  @Override
  public Object run() { 
    var ctx = RequestContext.getCurrentContext();
    ctx.getRequest();
  }

}
```
2. Post-filter is invoked just before Zuul sends the response back to the client. Used for logging, auditing.
3. Route filter are used for dynamic routing, e.g. blue-green testing.
