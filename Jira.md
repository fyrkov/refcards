### Jira API examples
Get meta
`curl --request GET --header 'Authorization: Basic =' --url https://jira.int.host.net/jira/rest/api/latest/issue/createmeta?projectKeys=SPRING`

Get projects
`curl --request GET --header 'Authorization: Basic =' --url https://jira.int.host.net/jira/rest/api/latest/project/SPRING`

Get board
`curl --request GET --header 'Authorization: Basic =' --url https://jira.int.host.net/jira/rest/agile/1.0/board/4767`

Get Sprints
`curl --request GET --header 'Authorization: Basic =' --url https://jira.int.host.net/jira/rest/agile/1.0/board/4767/sprint?state=active`

Get issue
`curl --request GET --header 'Authorization: Basic =' --url https://jira.int.host.net/jira/rest/api/latest/issue/SPRING-1648`

GET issue types
`curl --request GET --header 'Authorization: Basic =' --url https://jira.int.host.net/jira/rest/api/latest/issue/createmeta/{projectIdOrKey}/issuetypes`


Working POST
```
curl \
--request POST \
--header "Authorization: Basic $(echo -n sys.spring-jenkins:mQnKxta0kIq7ejki8ojrfZrdy8by | base64)" \
--header "Content-Type: application/json"  \
--data "{ \
\"fields\": { \
\"project\": { \"key\": \"SPRING\" }, \
\"summary\": \"Testing Jira REST API\", \
\"description\": \"Creating of an issue using project keys and issue type names using the REST API\", \
\"issuetype\": { \"name\": \"Work Item\" }, \
\"assignee\": { \"name\": null } \
} \
}" \
--url  https://jira.int.host.net/jira/rest/api/latest/issue/
```
