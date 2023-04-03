##  NFS или это не про гонки

**NFS (Network File System)** (дословно - сетевая файловая система) - протокол для подключения(монтирования) файловых систем по сети.

Преимущества: 
1. Данные можно хранить на одной машине и предоставлять доступ других ПК - используется меньше дискового пространства
2. Можно сделать один NFS сервер, создать там директорию и хранить данные там - использовать один каталог с разных устройств
3. Можно совместо использовать один экземпляр CDROM и/или USB 

```
Работает по принципу сервер-клиент.

Нативный - работает "из коробки" в семействе систем Linux.

Есть возможность настроить аутентификацию kerberos (NFSv4)
```

Настройка выполняется в конфигкрационном файле /etc/exports.

<img width="734" alt="Снимок экрана 2023-03-13 в 15 35 45" src="https://user-images.githubusercontent.com/66735783/224703624-275d7dfe-9c5b-4ec1-9d03-026daedac089.png">


### Настройка NFS-сервера

1. Устанавливаем пакеты
```
apt-get install nfs-kernel-server
```
2. Создаем директории
```
mkdir -p /opt/nfs/ro /opt/nfs/rw
chmod 777 /opt/nfs
chmod 775 -R /opt/nfs/ro
chmod 777 -R /opt/nfs/rw
chown nobody:nogroup  -hR /opt/nfs
```
3. Настраиваем правила доступа в файле /etc/exports
```
/opt/nfs/rw    lan-ikt-network(rw,sync,insecure)
/opt/nfs/ro    lan-ikt-network(ro,sync,insecure)
```
4. Применяем правила и перезапускаем сервис
```
service nfs-kernel-server restart
showmount -e
exportfs -a
```