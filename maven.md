### Maven

Maven phases:
- validate - validate the project is correct and all necessary information is available
- compile - compile the source code of the project
- test - test the compiled source code using a suitable unit testing framework. These tests should not require the code be packaged or deployed
- package - take the compiled code and package it in its distributable format, such as a JAR.
- integration-test - process and deploy the package if necessary into an environment where integration tests can be run
- verify - run any checks to verify the package is valid and meets quality criteria
- install - install the package into the local repository, for use as a dependency in other projects locally
- deploy - done in an integration or release environment, copies the final package to the remote repository for sharing with other developers and projects

Download dependencies without doing anything else, `-U`=force update:\
`mvn dependency:resolve -U`

To download a single dependency:\
`mvn dependency:get -Dartifact=groupId:artifactId:version`

To run a single test case:\
`mvn -Dtest=<TestClass>#<TestMethod> test`

Install skipping tests:\
`mvn install -DskipTests`

This will skip also compiling tests:\
`mvn install -Dmaven.test.skip=true`

Show dependency tree:
```
mvn dependency:tree

// shows all resolved versions
mvn dependency:tree -Dverbose -Dincludes=[groupId]:[artifactId]:[type]:[version]

// shows effectively resolved version
mvn dependency:tree -Dincludes=org.springframework.sync
```
