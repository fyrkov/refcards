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
gradle dependencies --configuration scm
gradle dependencies
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
