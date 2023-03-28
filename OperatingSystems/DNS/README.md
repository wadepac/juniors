## DNS или Служба доменных имен


### dnsmasq
1. Обновим пакеты и ОС

```
sudo apt-get -y update

sudo apt-get -y upgrade
```

2. Установим dnsmasq, resolvconf, net-tools

```
apt-get -y install dnsmasq resolvconf net-tools
```

3. Отредактируйте файл конфигурации /etc/dnsmasq.conf следующим образом

```
no-resolv
no-hosts
listen-address=0.0.0.0
bind-interfaces

domain=msk.skills

server=/ya.ru/77.88.8.8
server=8.8.8.8

address=/compX-srv1.msk.skills/ip
address=/compX-srv2.msk.skills/ip
address=/compX-srv3.msk.skills/ip
address=/split.msk.skills/2.2.2.2
address=/split.msk.skills/3.3.3.3

cname=compX-srv3,web
cname=compX-srv1,logs

mx-host=msk.skills,compX-srv1
```
4. Перезапустим сервис
```
sudo service dnsmasq restart
```

5. Проверяем, что наша конфигурация работает
```
nslookup compX-srv3.msk.skills
dig compX-srv3.msk.skills 
```

### BIND9