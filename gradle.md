Run specific test
```
gradle test --tests *AwsSsmBootstrapIntegrationTest
```

Listing tasks
```
gradle tasks
```

Listing projects
```
gradle projects
```

Obtaining detailed help for tasks
```
gradle -q help --task libs
```

Building dependency tree
```
gradle dependencies
gradle dependencies --configuration scm

// for a sub-module
gradle subproject:dependencies

// for all sub-modules add a task in build.gradle
subprojects {
    task allDeps(type: DependencyReportTask) {}
}
gradle allDeps

```

Using the dependency insight report for a given dependency
```
gradle -q dependencyInsight --dependency commons-codec --configuration scm

// multi-module
gradle -q application:dependencyInsight --dependency spring-data-r2dbc --configuration compileClasspath
```

Debugging with IntelliJ:\
1.
```
gradle {task} --debug-jvm
```
2. Create `Run Configuration` from `Remote` template with defult pararmeters: localhost:5005

### Dependency locking
To ensure repeatable builds all gradle dependencies are locked to fixed versions in `gradle.lockfile` files.\
It means that each time the app is built the dependency resolution tasks must produce the same results as in `gradle.lockfile` files.\
If the result differs then the build fails, e.g.
```
Execution failed for task ':application:compileJava'.
> Could not resolve all files for configuration ':application:compileClasspath'.
   > Did not resolve 'ch.qos.logback.contrib:logback-jackson:0.1.4' which has been forced / substituted to a different version: '0.1.5'
```
In such a case, the lock file must be updated and committed to ensure that the version change of the dependency remains under explicit control.\
To update (or create) a lockfile for a given project run `dependencies --write-locks`, e.g:
```
gradle :application:dependencies --write-locks
```
Note that dependency locking detects only dynamic version changes like `2.+`, but not the "changing version" like `-SNAPSHOT`.\
Further info on dependency locking in Gradle: https://docs.gradle.org/current/userguide/dependency_locking.html


