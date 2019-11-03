### DOCKER REFERENCE https://docs.docker.com/reference/

Docker status
```
docker info
```

Start docker daemon on linux
```
sudo service docker start
```

Add current user to docker group to avoid using sudo each time
```
sudo usermod -aG docker ec2-user
```

Docker container official hub is hub.docker.com.\
Official root images without slash in name.\
Pulling image without a tag means pulling the latest.\
Pull a container
```
docker pull ubuntu
docker pull tomcat:<tag>
```

List pulled images
```
docker image ls
```

Run container from image and expose container port 8080 to outer port 80
```
docker container run -p 80:8080 -d tomcat
docker container run -it ubuntu
```
where `-d` for detached, `-it` to run interactively attached to terminal

List containers
```
docker container list
docker container list -a # -a for all including inactive
```

Delete all inactive containers
```
docker container prune
```

Stop container
```
docker container stop <id>
```

Ssh into docker container
```
docker container exec -it <id> bash
```

Get logs that container have produced
```
docker container logs <id>
docker container logs -f <id> # -f to follow log output
```

Create your own image from a container
```
docker container commit -a "comment" <id> <name>
```

Build an image from a dockerfile located in .
```
docker image build -t "name_of_the_image" .
```

======================================================

### Manage swarm (orchestration tool like Kubernetes)
Operates with "nodes" instead of one host machine.\
Containers get distributed between available nodes
```
docker swarm <command>
```
Iniializes a swarm and makes this host a manager
```
docker swarm init
```
Iniializes swarm on a host with multiple networks requires to set network explicitly
```
docker swarm init --advertise-addr <10.0.16.7>
```
Adding a worker to the swarm
```
docker swarm join --token <swarm_token> <address>
```

### Manage services
Services are containers inside a swarm
```
docker service <command>
```
Services in swarm require "overlay" type network, not "bridge". It works across multiple nodes
```
docker network create --driver overlay <network_name>
```
Use `service create` instead of `container run`
```
docker service create -d --network <my_network> -e <...> --name <service_name, e.g. database> <image>
```
To show that service has been started randomly somewhere on some host inside the swarm
```
docker service ls
```
Checking logs from service
```
docker service logs -f <service_name>
```

### Manage Swarm nodes
Nodes can be managers and workers. Workers can not execute some commands to manage the swarm.\
Manager nodes communicate with raft system: https://raft.github.io. Ususally managers number is N/2+1
```
docker node <command>
```
List hosts in this swarm
```
docker node ls
```

### Manage docker stacks. 
Stack is set of containers representing one whole application.\
Managing stack is like using docker-compose
```
docker stack <command>
```
Launch stack in swarm from `docker-compose.yaml`.\
No need to care of start order, because swarm will restart any service if it fails
```
docker stack deploy -c docker-compose.yaml "stack_name"
```
