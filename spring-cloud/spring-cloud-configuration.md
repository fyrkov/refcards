### Spring cloud configuration server

- segregates configuration from codebase
- allows environment specific properties
- uses spring profiles to resolve environment specific properties
- integrates with git (and cloud git providers like GitHub) allowing to enable version control for config repository
- can also have a filesystem based repository
- provides encryption mechanism for sensitive properties

Conguration server can pull configs from different types of sources, but mostly used with git.\
In this case conguration server works like a git client that can make a `git pull` from a remote git repo.\
By default server clones remote when configs are first requested.\
Server can be configured to clone the remote at startup with `cloneOnStart: true`.

From clients POV conguration server provides REST API endpoints
that follow the pattern `/{application}/{profile}[/{label}]`\
where `label` refers to a git label (commit id, branch name, or tag).\
Example of fetching certain configs:
```
curl http://localhost:10000/v1/config/eagle-employee-service/ecs-eagle-prod/master | jq . | grep -i --color envname

```
Configuration server can be synced with remote repo by:
- Creating a GitHub webhook that will push notifications to `POST /monitor` endpoint
- Setting `spring.cloud.config.server.git.refreshRate` to poll

### Usage

1. To start a config server add a dependency, e.g.
```
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-config-server</artifactId>
</dependency>
```

2. Use `@SpringBootApplication` and `@EnableConfigServer` on configuration class.

3. Set up `bootstrap.yml`, e.g.
```
spring:
  cloud:
    config:
      server:
        git: # tells to use git as backend system
          uri: <git-host>
          ...
...
```

4. When started config server will expose configs as REST endpoints, e.g.
`curl localhost:8888/<application>/<profile>`
- `application` maps to `spring.application.name` on the client side
- `profile` maps to `spring.profiles.active` on the client (comma-separated list)
- `label` for git-backed config server maps to a git label (commit id, branch name, or tag).

:exclamation: Mind that in Spring Boot configs lookup are hierarchical, i.e. first default profile is read, then env-specific.

5. Services read configs from config server like
```
spring:
  application: <name>
  cloud:
    config:
      uri: <config-server-host>
```

6. To enable symmetric encryption config server must start with  `ENCRYPT_KEY` env var. 
To encrypt a property use `curl POST <config_server_host>:8888/encrypt -d "mysecret"`.
Put the encrypted value with prepended `{cipher}` into the config file.
Config server will then read the encypted property, decrypt it and expose decypted to a service. 
However it is also possible to delegate decryption to a client service.
