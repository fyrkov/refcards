### Networking

#### Troubleshooting:
1. Check routing to host
```
ip route get 172.31.132.29
172.31.132.29 via 10.1.19.254 dev wlp5s0 src 10.1.17.31 uid 1000 
    cache 
```
2. Check routing rules
```
ip route list
default via 10.1.19.254 dev wlp5s0 proto dhcp metric 600 
10.1.16.0/22 dev wlp5s0 proto kernel scope link src 10.1.17.31 metric 600 
10.255.250.252 via 10.1.19.254 dev wlp5s0 
169.254.0.0/16 dev wlp5s0 scope link metric 1000
```

#### SSH
Port forwarding example in `~/.ssh/config`:
```
Host voyager-prod-bastion*
  Hostname voyager-prod-bastion.aws.zedconnect.com
  User ec2-user
  ForwardAgent yes
  AddKeysToAgent yes
  IdentityFile ~/.ssh/key.pem
  IdentitiesOnly yes
  LocalForward localhost:9993 voyager-db.prod.aws.zedconnect.com:3306
```

#### Curl & Wget

**Curl** is a "CLI browser".

`GET` examples:
```
curl -s \
-H "DevAuthorization: fleetadmin|fleetadmin_dev_1" \
"https://voyager-core.dev.internal.zedconnect.com/"

curl --insecure \
-H "Authorization: Bearer <token>" \
https://eagle.wkda-test.com/employee/ \
| jq -r
```
where `jq -r` to prettyprint json.

`POST` request with Parameters:
```
curl --data "firstName=John&lastName=Doe" https://yourdomain.com/info.php
```

`POST` with request body in the given file and specified headers:
```
curl -s -XPOST -H "DevAuthorization: fleetadmin|fleetadmin_dev_1" -H "Content-Type: application/json" "https://voyager-core.dev.internal.zedconnect.com/drivers/1/reports" -d@req.json
```

Download the file (may use `wget` instead):
```
curl -o archive.zip https://domain.com/file.zip
```
Or with `-O` for saving files with the same names as on the remote server:
```
curl -O https://domain.com/file.zip
```

Query HTTP Headers:\
`curl -I www.tecmint.com`

Use a Proxy:\
`curl -x proxy.yourdomain.com:8080 -U user:password -O http://yourdomain.com/yourfile.tar.gz`,\
where you can skip `-U user:password` if proxy does not require auth.

**Wget** retrieves content from web servers. `Wget` can continue iterrupted downloads, download multiple URLs or multiple files from URL, download with HTTP auth, work like a spider and other stuff. But still `curl` can do almost everthing that `wget` does and more

Download a single file from the Internet:\
`wget http://example.com/file.iso`

Download a file but save it locally under a different name:\
`wget ‐‐output-document=filename.html example.com`

**SCP** is a "secure copy", it allows files to be copied to, from, or between different hosts. It uses ssh for data transfer and provides the same authentication and same level of security as ssh.

Copy file from remote to local:\
`scp your_username@remotehost.edu:foobar.txt /some/local/directory`

Copy file from local to remote:\
`scp foobar.txt your_username@remotehost.edu:/some/remote/directory`


#### Troubleshooting utilities

To check connectivity to the host or reveal IP by the name of the host:\
`ping <host>`

To check IPs of the routers on the route and number of hops.\
Can be used to locate where the loss of data occurs, and probably the node is down.\
Can be used to locate slow nodes.\
`traceroute <ip address>`

Using telnet to test open ports:
`telnet <domainname or ip> [port]`

**iproute2** and **net-tools** packages.\
`iproute2` package now reaplces deprecated `net-tools`.

New `ip [opts] <object> <command>` is equivalent to old `ifconfig`.

The output of `ifconfig` or `ip -c link show` shows the list of network interfaces like:
- `lo` loopback
- `eth0` or `enpXsY` ethernet
- `wlan0` or `wlpXsY` wireless
- `vmnet` VM's interface working in bridge mode or NAT mode
- `br0` - bridge
- `virbr0` -  "Virtual Bridge 0" interface is used for NAT (Network Address Translation)
- `docker0` - docker interface

`ip` command objects can be:
- `address` to show addresses addigned to network interfaces
- `link` to show network interfaces
- others

To check assigned IPs, `-4` = IPv4 only, `a` = `address`, `-c` = color output:\
`ip -4 -c a`

Bring up or down network interface `eth0`:\
`ip link set eth0 [up|down]`\
`ifconfig eth0 [up|down]`

Посмотреть таблицу маршрутизации:
```
ip route
...
default via 192.168.0.1 dev wlp2s0 proto dhcp metric 600 
169.254.0.0/16 dev wlp2s0 scope link metric 1000 
192.168.0.0/24 dev wlp2s0 proto kernel scope link src 192.168.0.17 metric 600 
```
This is similar to `route -n` or `netstat -rn` which have following output:
```
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
0.0.0.0         192.168.0.1     0.0.0.0         UG        0 0          0 wlp2s0
169.254.0.0     0.0.0.0         255.255.0.0     U         0 0          0 wlp2s0
192.168.0.0     0.0.0.0         255.255.255.0   U         0 0          0 wlp2s0
```
Where flags `G` = gateway (router), `U` = route is up, `H` = host route (exact IP match).
First row means route all packages from all hosts to 192.168.0.1.
Second and third tell route all packages from 169.245.0.0/16 and 192.168.0.0/24 directly on to `wlp2s0` interface.

**Netstat** command displays various network related information

Show gateway info, subnet mask & interface it is going through:\
`netstat -rnv`

Show all ports that are listening currently:\
`netstat -l`

Show all [tcp, udp] ports:\
`netstat -a[t,u]`

Show statistics for each protocol:\
`netstat -s`

`netstat -p` will add the “PID/Program Name” to the netstat output. This is useful to identify which program is running on a particular port:
```
netstat -pt
...
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address           Foreign Address       State       PID/Program name
tcp        1      0 ramesh-laptop.loc:47212 192.168.185.75:www    CLOSE_WAIT  2109/firefox
tcp        0      0 ramesh-laptop.loc:52750 lax:www               ESTABLISHED 2109/firefox
```

Find out on which port a program is running, for example `ssh`:\
`netstat -ap | grep ssh`

Find out which process is using a particular port:\
`netstat -an | grep ':80'`

Show the list of network interfaces (similar to `ifconfig`):\
`netstat -ie`

**Nmcli** is a cli tool for controlling NetworkManager.It is used to create, display, edit, delete, activate, and deactivate network connections, as well as control and display network device status.

Show the list of network interfaces:\
`nmcli device`

Show info on specific interface:\
`nmcli device show <interface>`

Show connections active/inactive:\
`nmcli connection`

**Netcat** or `nc` is a networking utility for debugging and investigating the network.
This utility can be used for creating TCP/UDP connections and investigating them:

```
nc -l 2389 // nc is now listening on port 2389 for a connection
nc localhost 2389 // connecting
```
There should now be a connection between the ports. Anything typed at the second console will be concatenated to the first, and vice-versa.

Examples of data transer:
```
nc -l 1234 > filename.out
nc host.example.com 1234 < filename.in
```

Port scanning:
```
nc -zv host.example.com 20-30
Connection to host.example.com 22 port [tcp/ssh] succeeded!
Connection to host.example.com 25 port [tcp/smtp] succeeded!
```

Cloning user home directory over net. Users should have the same UID, GID. Check with `id` and modify with:
```
sudo usermod --uid 1234 your-username
sudo groupmod --gid 1234 your-username
```

On source machine:\
`tar cz -C/home $(whoami) | nc -l -p 8888 -w 10 -vv`\
where `-l`= listen, `-p` = on port, `-w` = wait for secs, `-vv` = verbose.

On target machine:\
`nc -vv <ip_address_of_source_machine> 8888|tar xzp -C/home`\

**Tcpdump** traces every single transaction leaving/coming.\
To dump traffic of a specified interface:\
`tcpdump -i wlp2s0`

**Ethtool** shows info about network interface (link up/down, supported modes, speed, etc):\
`ethtool enp1s0`

**Nslookup** resolves hostname to ip and vice versa.\
`nslookup <hostname>` shows local DNS and IPs for resolved hostname.

**Dig** does practically the same: `dig <hostname>`


#### HTTPD Webserver
Package name - `httpd`\
Service:
```
systemctl enable httpd
systemctl restart httpd
```
Files: 
- `etc/http/conf/httpd.conf` - config
- `var/www/html/index.html` - main page`
- `var/log/httpd`
