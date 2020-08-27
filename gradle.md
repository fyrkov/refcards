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
```
