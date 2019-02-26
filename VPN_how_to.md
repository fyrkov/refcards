## VPN over VPS setup

### VPS
Requirements:
- Virtualization type: `KVM` - OK, `OpenVZ` - not OK.
- Good bandwidth and traffic limit
- CPU - 1 core, RAM - 0.5Gb min, storage - min
- Static IP preferably
- Location nearby

### OpenVPN setup
Starting OpenVPN in the background with `<OpenVPN_config>.ovpn` config file

```
apt-get -y install openvpn
touch /etc/openvpn/credentials
printf '%s\n' 'username' 'password' > /etc/openvpn/credentials
sed -i 's/auth-user-pass/auth-user-pass \/etc\/openvpn\/credentials/g' /etc/openvpn/<OpenVPN_config>.ovpn
nohup openvpn --config /etc/openvpn/<OpenVPN_config>.ovpn &
```
Stopping
```
sudo killall openvpn
```

### Wireguard setup

#### 1. Install Wireguard on both server and client machines
```
sudo add-apt-repository ppa:wireguard/wireguard
sudo apt-get update
sudo apt-get install wireguard
```

#### 2. Enable IP Forwarding
```
sudo nano /etc/sysctl.conf
```
Ищем строку `net.ipv4.ip_forward = 0` и заменяем 0 на 1.\
Применяем настройки `sudo sysctl -p`

#### 3. Generate 4 keys
```
cd /etc/wireguard/
sudo -i
umask 077
wg genkey | tee server_private_key | wg pubkey > server_public_key
wg genkey | tee client_private_key | wg pubkey > client_public_key
```

#### 4. Create wireguard server config
```
cd /etc/wireguard/
nano wg0.conf
```
Paste following: 
```
[Interface]
Address = 192.168.2.1/32
SaveConfig = true
PrivateKey = <insert server_private_key>
ListenPort = <port>

[Peer]
PublicKey = <insert client_public_key>
AllowedIPs = 192.168.2.2/32
```

#### 5. Create wireguard client config
```
cd /etc/wireguard/
nano wg0-client.conf
```
Paste following: 
```
[Interface]
Address = 192.168.2.2/32
PrivateKey = <client_private_key>
DNS = 192.168.2.1

[Peer]
PublicKey = <server_public_key>
Endpoint = <public_server_ip:<port>
AllowedIPs = 0.0.0.0/0
PersistentKeepalive = 25 
```

Cooments:
- allowedips = 0.0.0.0/0 -завернет весь траффик клиента в тунель
- dns - это на сервере стоит unbound dns для пущей секурности
- endpoint - внешний адрес сервера

#### 6. Setup DNS server
```
apt-get install unbound
curl -o /var/lib/unbound/root.hints https://www.internic.net/domain/named.cache
chown -R unbound:unbound /var/lib/unbound
cd /etc/unbound/unbound.conf.d
nano unbound_srv.conf
```
Paste following:
```
server:
  num-threads: 4
  verbosity: 1
  root-hints: "/var/lib/unbound/root.hints"

  interface: 0.0.0.0
  max-udp-size: 3072

  access-control: 0.0.0.0/0        refuse
  access-control: 127.0.0.1        allow
  access-control: 192.168.2.0/24   allow

  private-address: 192.168.2.0/24

  hide-identity: yes
  hide-version: yes

  harden-glue: yes
  harden-dnssec-stripped: yes
  harden-referral-path: yes

  unwanted-reply-threshold: 10000000

  val-log-level: 1
  cache-min-ttl: 1800 
  cache-max-ttl: 14400
  prefetch: yes
  prefetch-key: yes 
```
Then
```
systemctl restart unbound
systemctl enable unbound
```
Check if the service is running with `systemctl status unbound`. You should see a green active (running) text.

Check if DNS is working with `nslookup www.google.com 192.168.2.1`. The name should be resolved.

#### 7. Setup Firewall (IP Tables)
```
iptables -A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
iptables -A FORWARD -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
iptables -A INPUT -p udp -m udp --dport 54321 -m conntrack --ctstate NEW -j ACCEPT
iptables -A INPUT -s 192.168.2.0/24 -p tcp -m tcp --dport 53 -m conntrack --ctstate NEW -j ACCEPT
iptables -A INPUT -s 192.168.2.0/24 -p udp -m udp --dport 53 -m conntrack --ctstate NEW -j ACCEPT
iptables -A FORWARD -i wg0 -o wg0 -m conntrack --ctstate NEW -j ACCEPT
iptables -t nat -A POSTROUTING -s 192.168.2.0/24 -o eth0 -j MASQUERADE

apt-get install iptables-persistent // Select NO if prompts about saving rules
netfilter-persistent save
systemctl enable netfilter-persistent
```

#### 8. Reboot server
```
reboot
```
Re-ssh to the server.\
Check if unbound is running and the iptable rules are loaded:
```
sudo -i
systemctl status unbound
iptables -L
```

#### 9. Start the Wireguard on the server
```
chown -v root:root /etc/wireguard/wg0.conf // If necessary
chmod -v 600 /etc/wireguard/wg0.conf // If necessary
wg-quick up wg0
systemctl enable wg-quick@wg0.service  // So the interface is created each time after reboot
```

#### 10. Start the Wireguard on the client
```
sudo wg-quick up wg0-client
```
Check with `sudo wg show`. The output should be like following:
```
interface: wg0-client
public key: FwdTNMXqL46jNhZwkkzWsyR1AIlGX66vRWe1HFSemHw=
private key: (hidden)
listening port: 39451
fwmark: 0xca6c
peer: +lb7/6Nn8uhlA/6fjT3ivfM5fWKKQ2L+stX+dSq18CI=
endpoint: 165.227.120.177:51820
allowed ips: 0.0.0.0/0
latest handshake: 49 seconds ago
transfer: 11.41 MiB received, 862.25 KiB sent
persistent keepalive: every 21 seconds
```

#### 11. Stop the Wireguard on the client
```
sudo wg-quick down wg0-client
```

#### Troubleshouting
Checks:
- https://ipecho.net/
- http://dnsleak.com/

If `/usr/bin/wg-quick: line 31: resolvconf: command not found`\
on `sudo wg-quick up wg0-client`\
then `sudo apt install openresolv` (Ubuntu 18 issue)


#### Links
- https://habr.com/ru/post/432686/
- https://golb.hplar.ch/2018/10/wireguard-on-amazon-lightsail.html
- https://golos.io/vpn/@dj-losos/wireguard-vpn
- https://pikabu.ru/story/sam_sebe_vpn_5897033

