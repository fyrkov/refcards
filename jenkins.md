#### Installation and start up
Download a jar https://jenkins.io/ and
```
java -jar jenkins.war
```

Another way is to download docker image and run it 
```
docker pull jenkins/jenkins
docker run -p 8080:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts
```

By default jenkins stores all data in it's home directory `~/.jenkins`.\
Each job stores builds in `~/.jenkins/workspace/{jobName}/`.

It is possible to specify JDK version in  `Manage Jenkins > Global Tool Configuration > JDK`
