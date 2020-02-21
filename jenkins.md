#### Installation and start up
Download a jar https://jenkins.io/ and
```
java -jar jenkins.war
```

Another way is to download docker image and run it with volume mounted
```
docker pull jenkins/jenkins:lts
docker run -p 8080:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts
```

By default jenkins stores all data in it's home directory `~/.jenkins`.\
Each job stores builds in `~/.jenkins/workspace/{jobName}/`.

It is possible to specify JDK version in  `Manage Jenkins > Global Tool Configuration > JDK`

Jenkins job is stored as `config.xml` inside `.jenkins/jobs/{jobName}`

#### Pipeline job
Unlike freestyle job pipeline job is based on groovy scripts and is split in stages.

Build can run on agents/slaves, not only master.

Useful:
- `Pipeline Syntax > Snippet Generator`
- `Pipeline Syntax > Global Variable Reference`
- List of pipeline compatible plugins: https://github.com/jenkinsci/pipeline-plugin/blob/master/COMPATIBILITY.md

Running steps in parallel on different nodes is possible with cloning the workspace with
```
stash name: `everything`
    excludes: 'test-results/**'
    includes: '**'
```
and then 
```
unstash 'everything'
```

*Blue Ocean* is an experimantal UI wich is good for parallel pipelines
