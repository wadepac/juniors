# Wireguard

## Wireguard VPN Server (Debian 10)

### Install

```unix
sh -c "echo 'deb http://deb.debian.org/debian buster-backports main contrib non-free' > /etc/apt/sources.list.d/buster-backports.list"
apt update
apt install wireguard
```

### Configure

```unix
cd /etc/wireguard
umask 077; wg genkey | tee privatekey | wg pubkey > publickey
cat privatekey # copy output
nano /etc/wireguard/wg0.conf
```
#### wg0.conf file contents

```vim
[Interface]
## VPN server private IP address ##
Address = 192.168.0.1/24
 
## VPN server port ##
ListenPort = 51820
 
## VPN server's private key i.e. /etc/wireguard/privatekey ##
PrivateKey = eEvqkSJVw/7cGUEcJXmeHiNFDLBGOz8GpScshecvNHU
 
## Save and update this config file when a new peer (vpn client) added ##
SaveConfig = true

## Iptables rules (ens33 - your public interface)
PostUp = iptables -A FORWARD -i %i -j ACCEPT; iptables -t nat -A POSTROUTING -o ens33 -j MASQUERADE
PostDown = iptables -D FORWARD -i %i -j ACCEPT; iptables -t nat -D POSTROUTING -o ens33 -j MASQUERADE
```
### Enable and start service
```unix
systemctl enable wg-quick@wg0
systemctl start wg-quick@wg0
```

## Wireguard VPN Client (debian 10)

### Install

```unix
sh -c "echo 'deb http://deb.debian.org/debian buster-backports main contrib non-free' > /etc/apt/sources.list.d/buster-backports.list"
apt update
apt install wireguard
```

### Configure

```unix
cd /etc/wireguard
umask 077; wg genkey | tee privatekey | wg pubkey > publickey
cat privatekey # copy output
nano /etc/wireguard/wg0.conf
```
#### wg0.conf file contents

```vim
[Interface]
## This Desktop/client's private key ##
PrivateKey = uJPzgCQ6WNlAUp3s5rabE/EVt1qYh3Ym01sx6oJI0V4
 
## Client ip address ##
Address = 192.168.10.2/24
 
[Peer]
## Debian 10 server public key ##
PublicKey = qdjdqh2pN3DEMDUDRob8K3bp9BZFJbT59fprBrl99zM
 
## set ACL ##
AllowedIPs = 192.168.0.0/24
 
## Your Debian 10 server's public IPv4/IPv6 address and port ##
Endpoint = 172.105.112.120:51820
```
### Enable and start service
```unix
systemctl enable wg-quick@wg0
systemctl start wg-quick@wg0
```

### Add peer to VPN server
#### In your VPN server terminal:

```unix
wg set wg0 peer uJPpuCQ6WNlAUp3s5cdtE/EVt1qYh3Ym01sx6oJI0V4 # client public key allowed-ips 192.168.10.2
```
