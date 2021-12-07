Loopback interfaces

nmcli add connection.id lo1 connection.interface-name lo1 type dummy connection.autoconnect yes ipv4.method manual ipv4.address address/prefix


Nftables nat rules
nft add table ip nat
Masquerade nat
nft add rule nat postrouting oif interface masquerade


nft add table ip nat
nft add chain ip nat postrouting ‘{type nat hook postrouting priority 0;}’
nft add rule ip nat postrouting meta oifname <interface> masquerade

nft list ruleset > /etc/nftables/init.nft
nano /etc/nftables/init.nft
    flush ruleset #at the top 
nano /etc/sysconfig/nftables
    include “/etc/nftables/init.nft”
systemctl enable nftables --now

DNAT
nft add chain ip nat prerouting ‘{type nat hook prerouting priority 1;}’
nft add rule ip nat prerouting iifname ens192 udp dport { 53 } dnat 172.16.20.10
nft list ruleset > /etc/nftables/init.nft

NGINX
yum -y install nginx
openssl req -new -nodes -days 365 -newkey rsa:2048 -keyout /path -out /path

dhcp-relay

yum -y install dhcp-relay
cp /lib/systemd/system/dhcrelay.service /etc/systemd/system
nano /etc/systemd/system/dhcrelay.service
    ExecStart=/usr/sbin/dhcrelay -d --no-pid 172.16.50.2
systemctl daemon-reload
systemctl enable dhcrelay.service --now

Useful commands:
scp -r /.../.../ login@ip:/.../.../  - recursive copying


Packages:

dns = bind bind-utils
dhcp = dhcp-server  dhcp-relay
Apache = httpd
Php = php
CLI Browser = lynx(PowerTools.repo)

ipsec

nano /etc/ipsec.conf
    auto=start
    authby=secret
    type=tunnel
    ike=3des-sha1;modp2048
    phase2=esp
    phase2alg=aes-sha2;modp2048
    left=localip
    leftprotoport=gre
    right=remoteip
    rightprotoport=gre

nano /etc/ipsec.d/gre.secrets
localip    remoteip    :    PSK “password”

systemctl enable ipsec --now
ipsec status # if established = > all ok
syslog
 #server
yum -y install rsyslog
mkdir -p /opt/logs/{your,directories,x,y}
nano /etc/rsyslog.conf
    module(load=”imudp”) #uncomment this line
    input(type=”imudp” port=”514”) #uncomment this line
     #
#RULES
    # before other rules add
    if ($hostname == “L-SRV” and $syslogfacility-text == “auth” ) 
        then {
            action (type=”omfile” file=”/opt/logs/L-SRV/auth.log”)
}
if ($hostname == “l-fw”) 
        then {
            action (type=”omfile” file=”/opt/logs/L-FW/err.log”)
}
    rsyslogd -N 1
    service rsyslog restart
#rsyslog-client L-FW
 nano /etc/rsyslog.conf
#
#RULES
# change
*.err            @172.16.20.10

rsyslogd -N 1
service rsyslog restart
