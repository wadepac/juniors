## DNS или Служба доменных имен

### Порядок источников для разрешения имён (приоритет файла hosts и DNS)

Файл /etc/nsswitch.conf (Name Service Switch configuration file) - это конфигурационный файл переключения служб имён. Он устанавливает настройки службы преобразования имён хостов и доменных имён, а так же многие другие, но сейчас не об этом.

Нам необходимо изменить приоритет файла hosts и службы DNS
Для этого в файле /etc/nsswitch.conf ищем строку:
```
hosts:          files dns
```
И меняем местами files и dns, чтобы получить:
```
hosts:          dns files
```

### Настройка клиентов для использования dns
1. Обновим пакеты и ОС

```
sudo apt-get -y update

sudo apt-get -y upgrade
```

2. Установим resolvconf, net-tools

```
apt-get -y install resolvconf net-tools
```

3. Перезагрузим сетевые сервисы
```
sudo service networking restart
```


### Настройка сервер DNSна базе dnsmasq
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