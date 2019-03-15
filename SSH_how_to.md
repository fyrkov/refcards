## SSH how-to

### Basics

Connecting to remote host:\
`ssh remote_username@remote_host`

Connecting to remote host with a key with port other than 22:\
`ssh -i ~/.ssh/id_rsa -p <port> <username>@<remote_host>`

Sshd server should already be running. Otherwise:\
`sudo service ssh start`\
or\
`sudo systemctl start ssh`

Key-based auth works by creating a pair of keys: private and public.
The private key is located on the client machine and kept secret (must have `700` access).
The public key can be placed on any server you wish to access.
When a client attempts to connect using a key-pair, the server will use the public key to create a message for the client computer that can only be read with the private key.

Generating new ssh key where -t=algorithm -b=bits -C=comment(is not used in common ssh):\
`ssh-keygen -t rsa -b 4096 -C "your_email@example.com"`

For pass-phrase protected keys better use ssh-agent.\
Start the ssh-agent in the background:\
`eval "$(ssh-agent -s)"`
Add your SSH private key to the ssh-agent:\
`ssh-add ~/.ssh/id_rsa`

### SSH tunneling

( <intra-site.com> --- <work_host> ) --- <home_host> --- <google.com>

#### Local port forwarding
Task: access a host <google.com> which is not accessible in this local network (probably closed by a firewall) with help of another host <home_host> with running sshd server:
- `<work_host>$ ssh -L 9001:google.com:80 home` 
- http://localhost:9001 in the web browser on <work_host> machine will open google.com

#### Remote port forwarding 
This works like VPN.\
Task: gvie access to the database host `intra-site.com` which is not accessible outside.
- `<work_host>$ ssh -R 9001:intra-site.com:80 home`
- http://localhost:9001 in the web browser on <home_host> machine will open intra-site.com

The main difference is the direction of desired connection:
local forwarding:     --->\
ssh_client            --->        ssh_server\
remote forwarding:    <---\


#### Connecting to a database behind a firewall
Another good example is if you need to access a port on your server which can only be accessed from localhost and not remotely.

An example here is when you need to connect to a database console, which only allows local connection for security reasons. Let’s say you’re running PostgreSQL on your server, which by default listens on the port 5432.

`$ ssh -L 9000:localhost:5432 user@example.com`
The part that changed here is the localhost:5432, which says to forward connections from your local port 9000 to localhost:5432 on your server. Now we can simply connect to our database.

`$ psql -h localhost -p 9000`