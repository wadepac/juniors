## NAT

### Настройка NAT в Debian/Ubuntu

#### nftables
1. Установка пакетов
```
apt install nftables
```
2. Редактируем файл /etc/nftables.conf и создаем в нем нашу "цепочку" 
```
    table ip nat {
        chain postrouting {
            type nat hook postrouting priority 100;
            ip saddr <ваша сеть/маска> oif <внешний интерфейс> masquerade
        }
    }
```
3. Перезапускаем сервер и добавляем в автозагрузку
```
systemctl enable nftables
```