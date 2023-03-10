


Users
user script (linux)
apt-get install sudo
nano usr.sh
#!/bin/bash
num=1
groupadd workers1
groupadd workers2
while [ “$num” -lt 11 ]
do
useradd -m -d /home/user$num -p P@ssw0rd -s /bin/bash user$num
echo “user$num:P@ssw0rd” | chpasswd
if [ “$num” -lt 6 ]
then
usermod -a -G workers1 user$num
else
usermod -a -G workers2 user$num
fi
echo “user$num ALL = (ALL:ALL) ALL” >> /etc/sudoers
let “ num +=1 ”
done
chmod 777 usr.sh
./usr.sh #после этого чекаем


Security
openvpn
#client
apt-get install openvpn*
nano /etc/openvpn/client.conf
dev tun
proto udp
remote 1.1.0.1 1194
ifconfig 10.8.0.2 10.8.0.1
secret /etc/openvpn/secret.key
cipher AES-256-CBC
comp-lzo no
verb 3
;mute 20
dhcp-option DNS 192.168.10.100
script-security 2
up /etc/openvpn/update-resolv-conf
down /etc/openvpn/update-resolv-conf
openvpn /etc/openvpn/client.conf
service openvpn restart # лучше reboot
#server
iptables
iptables -t nat -A POSTROUTING -o ens32 -j MASQUERADE #nat с outbound интерфейсом ens32
iptables-save > /etc/iptables/rules.v4
https://1cloud.ru/help/linux/nastrojka_linus-firewall_iptables


https://linux-admins.ru/article.php?id_article=62&article_title=%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0%20%D0%B8%20%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20Dnsmasq

nftables
apt install nftables
vim /etc/nftables.conf
    table ip nat {
        chain postrouting {
            type nat hook postrouting priority 100;
            ip saddr 192.168.20.0/29 oif ens224 masquerade
        }
    }
systemctl enable nftables
systemctl start nftables
IPSec

Folders&Files
ftp
apt-get install vsftpd* ftp* lynx
#11-14
nano /etc/vsftpd.conf
    listen=YES
    listen_ipv6=NO
    anonymous_enable=YES
    no_anon_password=YES
    local_enable=NO
nano /srv/ftp/index.html #пишем сюда всякую фигню код страницы сайта
service vsftpd restart
ftp yourip #чекаем
lynx ftp://yourip #чекаем
#14-16
adduser --home /opt/storage ftpuser
nano /etc/vsftpd.conf
    listen=YES
    listen_ipv6=NO
    anonymous_enable=NO
    local_enable=YES
    write_enable=YES
    dirmessage_enable=YES
    use_localtime=YES
    xferlog_enable=YES
    connect_from_port_20=YES
    pasv_enable=YES
    pasv_min_port=20500
    pasv_max_port=20510
    pasv_address=178.207.179.6
    secure_chroot_dir=/var/run/vsftpd/empty
    pam_service_name=vsftpd
    utf8_filesystem=YES


samba
#server
apt-get install samba*
nano /etc/samba/smb.conf
    [global]
    workgroup=WORKGROUP
    interfaces = ens33,lo
    [home]
[ftpstorage]
    comment = storage
    browseable = yes
    security = user
    path = /opt/storage
    public = yes
    guest ok = no
    read only = no
    valid users = ftpuser
    writeable = yes
    create mask = 0777
    directory mask =  0777
    #[printers]
    #[print$]
smbpasswd -a ftpuser
smbpasswd -e ftpuser
chmod 775  /opt/storage
chmod 775  /opt/storage/*
service smbd restart

#client
apt-get install samba * cifs-utils
nfs
#server
apt-get install nfs-kernel-server
mkdir -p /opt/nfs/ro /opt/nfs/rw
chmod 777 /opt/nfs
chmod 775 -R /opt/nfs/ro
chmod 777 -R /opt/nfs/rw
chown nobody:nogroup  -hR /opt/nfs
nano /etc/exports
    /opt/nfs/rw    192.168.1.0/24(rw,sync,insecure)
    /opt/nfs/ro    192.168.1.0/24(ro,sync,insecure)
service nfs-kernel-server restart
showmount -e
exportfs -a
#client
apt install nfs-common
mount -t nfs4 192.168.2.8:/opt/nfs/rw /home/skill39/Desktop/nfs_rw/ -o rw,user=skill39
mount -t nfs4 192.168.2.8:/opt/nfs/ro /home/skill39/Desktop/nfs_ro/ -o ro,user=skill39
nano /etc/fstab
192.168.2.8:/opt/nfs/ro /home/skill39/Desktop/nfs_ro/ nfs4  ro    0    0
192.168.2.8:/opt/nfs/rw /home/skill39/Desktop/nfs_rw/ nfs4  rw    0    0






Logs
 syslog
 #server
mkdir -p /opt/logs/{your,directories,x,y}
nano /etc/rsyslog.conf
    module(load=”imudp”) #uncomment this line
    input(type=”imudp” port=”514”) #uncomment this line
     #
#RULES
    # before other rules add
if ($hostname == “GW” or $hostname == “GW.JunWin.wsr”) 
        then {
            action (type=”omfile” file=”/opt/logs/errors.log”)
}
if ($hostname == “LINCLI” and $syslogseverity <= 3 ) 
        then {
            action (type=”omfile” file=”/opt/logs/errors.log”)
}
    rsyslogd -N 1
    service rsyslog restart
  #vyos
 set system syslog host 192.168.10.102 facility all level notice
 set system syslog host 192.168.10.102 facility all level warning
 set system syslog host 192.168.10.102 facility all level err
 set system syslog host 192.168.10.102 facility all level crit
 set system syslog host 192.168.10.102 facility all level alert
 set system syslog host 192.168.10.102 facility all level emerg

#rsyslog-client
 nano /etc/rsyslog.conf
#
#RULES
# change
*.err            @192.168.10.102
*.crit            @192.168.10.102
*.alert            @192.168.10.102
*.emerg        @192.168.10.102
rsyslogd -N 1
service rsyslog restart
logger -p auth.err Test “AUTH.ERR” from LINSRV


Web
apache2

apt-get install apache2*
cp /etc/apache2/sites-available/000-...  /etc/apache2/sites-available/yoursite.conf
a2dissite 000-...
nano  /etc/apache2/sites-available/yoursite.conf #вносим нужные правки
#ipv4 site
    <VirtualHost *:80>
    ServerAdmin admin@your_email_domain
    ServerName your_domain
    ServerAlias www.your_domain
    DocumentRoot /var/www/
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
ln -s /etc/apache2/sites-available/yoursite.conf /etc/apache2/sites-enabled/yoursite.conf
a2ensite yoursite.conf
service apache2 reload #ну и типо не забываем закинуть index в documentroot директорию и потом рестартнуть

nginx
apt-get install nginx*
nano /etc/nginx/sites-available/default
 #ipv4 site
server {
        listen 80 ;
        root  /var/www/ ;
         index index.html ;
        server_name web.office.wsr web.office.ru ;
         location /     {
    try_files $uri $uri/ =404;
}
if ($host != “web.office.wsr”) {
    return 404;
}
}
#ipv6 site
server {
        listen [::]:80 ipv6only=on ;
        root  /var/www/ip6/ ;
         index index.html ;
        server_name web6.office.wsr web6.office.ru ;
         location / {
    try_files $uri $uri/ =404;
}
}
mkdir /var/www/ip6
nano … # редактируем/создаем index файлы в указанных в конфиге директориях
nginx -t  #проверяем conf файл
service nginx restart 
ping web.office.wsr # или другое dns-имя и после этого чекаем через lynx или с клиента в браузере

nginx (day 3)
openssl req -new -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/conf.d/server.key -out /etc/nginx/conf.d/server.crt
nano /etc/nginx/sites-available/default
 #ipv4 site
server {
        listen 80 ;
        server_name web.office.wsr web.office.ru ;
        if ($host != “web.office.wsr”) {
    return 404;
}
return 301 https://$host$request_uri ;
}
server {
    listen 443 ssl;
    root  /var/www/ ;
        server_name web.office.wsr web.office.ru ;
        
ssl_certificate /etc/nginx/conf.d/server.crt ;
ssl_certificate_key /etc/nginx/conf.d/server.key ;
        
 location / {
    try_files $uri $uri/ =404;
}

}
#ipv6 site
server {
        listen [::]:80 ipv6only=on ;
        server_name web6.office.wsr web6.office.ru ;
return 301 https://$host$request_uri ;
}
server {
        listen [::]:443 ssl ;
        root  /var/www/ip6/ ;
        server_name web6.office.wsr web6.office.ru ;
    
        ssl_certificate /etc/nginx/conf.d/server.crt ;
ssl_certificate_key /etc/nginx/conf.d/server.key ;

         location / {
    try_files $uri $uri/ =404;
}
    }
nginx -t  #проверяем conf файл
service nginx restart 
lynx http://web/6.office.wsr/ru # чекаем, что есть редирект, и доступ к сайтам

Time
ntp
#vyos_server
set system ntp server 127.127.1.0
#client
apt-get install ntp ntpdate
nano /etc/ntp.conf
server 192.168.1.1
service ntp restart
service ntp stop
ntpdate -d 192.168.1.1
ntpq -p
service ntp start





























Network
dhcp-relay
#vyos
conf
set service dhcp-relay server x.x.x.x
set service dhcp-relay interface eth1
set service dhcp-relay interface eth2

dhcpv6-relay
#vyos
conf
set service dhcpv6-relay
set service dhcpv6-relay enable
set service dhcpv6-relay listen-interface eth2 address 3001:2:3:2:129
set service dhcpv6-relay upstream-interface eth2 address 3001:2:3:1::100
radvd + dhcpv6
#dhcpv6
nano /etc/dhcp/dhcpd6.conf
option dhcp6.name-servers 3001:2:3:c::1;
subnet6 3001:2:3:c::/64 {
    range6 3001:2:3:c::10 3001:2:3:c::20;    
}
nano /etc/default/isc-dhcp-server
INTERFACESv6=”ens33”
service isc-dhcp-server restart

#radvd
apt-get install radvd*
cp /usr/share/doc/radvd/examples/radvd.conf.example  /etc/radvd.conf
nano /etc/radvd.conf #удаляем лишнее оставляя 
interface ens33 { 
        AdvSendAdvert on;
        MinRtrAdvInterval 3; 
        MaxRtrAdvInterval 10;
        prefix 3001:2:3:c::/64 { 
                AdvOnLink on; 
                AdvAutonomous on; 
                AdvRouterAddr off; 
        };
}; #копипастим по мере надобности, изменяя prefix
service radvd restart
radvdump #запускаем на ipv6 клиенте и чекаем
f
ospf
#vyos
set protocols ospf area 0 network x.x.x.x/xx
#vyos
set protocols ospfv3 area 0.0.0.0 interface eth0
set protocols ospfv3 area 0.0.0.0 range 3001:2:3:1::/64 advertise
set protocols ospfv3 redistribute connected

set protocols ospf passive-interface eth1
set protocols ospf passive-interface eth2
ospfv3
Misc Services
LLDP + CDP
#vyos
set service lldp
set service lldp interface all
set service lldp legacy-protocols cdp
#debian
apt install lldpd
nano /etc/default/lldpd
    DAEMON_ARGS="-x -c"
systemctl restart lldpd

