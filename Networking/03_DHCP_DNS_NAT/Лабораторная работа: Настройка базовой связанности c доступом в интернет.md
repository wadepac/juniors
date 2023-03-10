## Топология
![l_01_topology](https://user-images.githubusercontent.com/66735783/137201913-f677e9f6-f2e6-4e51-9f67-c214ca2a50b7.png)


## Таблица адресации


|Отдел| VLAN | Сеть/маска сети|
| :---: | :---: | :---: |
|Department 1 | 10 | 10.10.1.0/24|
|Department 2 | 20 | 10.10.2.0/24|
|Relax_zone | 30 | 10.10.3.0/24|

|Устройство| VLAN | IP | 
| :---: | :---: | :---: |
| R1 | 10 | 10.10.1.254/24|
| | 20 | 10.10.2.254/24|
| | 30 | 10.10.3.254/24|
| | internet | 8.8.8.1/24
| WR | 30 | 10.10.3.253/24|

## Настройка на сетевых устройствах

### Базовая настройка

|SW2|SW1|R1|
| --- | --- | --- |
|```enable``` |```enable``` |```enable``` |
| ```conf t```|```conf t```|```conf t```|
|```hostname SW2```|```hostname SW1```| ```hostname R1```|
|```ip name-server 8.8.8.8```|```ip name-server 8.8.8.8```|```ip name-server 8.8.8.8```|
|```do copy running-config startup-config```|```do copy running-config startup-config```|```do copy running-config startup-config```|

### Настройка VLAN
|SW2|SW1|
| --- | --- |
|```enable``` |```enable``` |
| ```conf t```|```conf t```|
|```vlan 20```|```vlan 10```| 
|```name Department_2```|```name Department_1```| 
|```interface vlan 20```|```interface vlan 10```| 
|```no shutdown```|```no shutdown```|
| |```vlan 20```|
| |```name Department_2```|
| |```interface vlan 20```|
| |```no shutdown```|
| |```vlan 30```|
| |```name Relax_zone```|
| |```interface vlan 30```|
| |```no shutdown```|
|```do copy running-config startup-config```|```do copy running-config startup-config```|

### Настройка trunk
R1
```
enable
conf t
interface gi0/2
no shutdown
switchport mode trunk
do copy running-config startup-config
```


### Настройка access
|R-O2|R-O1|
| --- | --- |
|```enable``` |```enable``` |
| ```conf t```|```conf t```|
|```interface r fa0/1-24, gi0/1-2```|```interface gi0/2```| 
|```no shu```|```no shutdown```| 
|```switchport mode access```|```switchport mode access```| 
|```switchport access vlan 20```|```switchport access vlan 20```|
||```interface r fa0/1-10```|
||```no shutdown```|
||```switchport mode access```|
||```switchport access vlan 10```|
||```interface fa0/24```|
||```no shutdown```|
||```switchport mode access```|
||```switchport access vlan 30```|
|```do copy running-config startup-config```|```do copy running-config startup-config```|

### Настройка sub-интерфейсов на R-1
|R1|
| --- |
|```enable``` |
| ```conf t```|
|```interface gi0/2.10```|
|```no shutdown```|
|```encapsulation dot1q 10```|
|```ip address 10.10.1.254 255.255.255.0```|
|```interface gi0/2.20```|
|```no shutdown```|
|```encapsulation dot1q 20```|
|```ip address 10.10.2.254 255.255.255.0```|
|```interface gi0/2.30```|
|```no shutdown```|
|```encapsulation dot1q 30```|
|```ip address 10.10.3.254 255.255.255.0```|
|```interface gi0/2```|
|```no shutdown```|
|```do copy running-config startup-config```|

### R1 -> Internet
|R1|
| --- |
|```enable``` |
| ```conf t```|
|```interface gi0/1```|
|```no shutdown```|
|```ip address 8.8.8.1 255.255.255.0```|
|```do copy running-config startup-config```|

#### Настройка статического NAT
|R1|
| --- |
|```enable``` |
| ```conf t```|
|```access-list 13 permit 10.10.1.0 0.0.0.255```|
|```access-list 13 permit 10.10.2.0 0.0.0.255```|
|```access-list 13 permit 10.10.3.0 0.0.0.255```|
|```ip nat inside source list 13 interface gi0/1```|
|```interface gi0/2.10```|
|``ip nat inside```|
|```interface gi0/2.20```|
|```ip nat inside```|
|```interface gi0/2.30```|
|```ip nat inside```|
|```interface gi0/2```|
|```ip nat inside```|
|```interface gi0/1```|
|```ip nat outside```|
|```do copy running-config startup-config```|

### Настройка DHCP для Department_1 и Department_2
|R1|
| --- |
|```enable``` |
| ```conf t```|
|```ip dhcp pool Department1```|
|```network 10.10.1.0 255.255.255.0```|
|```default-router 10.10.1.254```|
|```dns-server 8.8.8.8```|
|```exit```|
|```ip dhcp pool Department2```|
|```network 10.10.2.0 255.255.255.0```|
|```default-router 10.10.2.254```|
|```dns-server 8.8.8.8```|
|```exit```|
|```ip dhcp excluded-address 10.10.1.254```|
|```ip dhcp excluded-address 10.10.2.254```|
|```do copy running-config startup-config```|

### Настройка сервера в сети Internet

![Screenshot 2021-10-13 225559](https://user-images.githubusercontent.com/66735783/137204413-9518ff01-fce8-47ac-a7c8-eec342994874.png)
![Screenshot 2021-10-13 225751](https://user-images.githubusercontent.com/66735783/137204431-c3639bab-947b-406c-a15d-da228e3a2e5f.png)

### Настройка на всех клиентских PC

![Screenshot 2021-10-13 225913](https://user-images.githubusercontent.com/66735783/137204521-faf60756-3c66-4741-be39-8d0ba36b4d15.png)
![Screenshot 2021-10-13 225848](https://user-images.githubusercontent.com/66735783/137204524-9fdda39c-5aa7-41f9-89b9-7b6844bc055f.png)

### Тестирование

| PC0 | PC7 |
| --- | --- |
|![Screenshot 2021-10-13 230015](https://user-images.githubusercontent.com/66735783/137204672-1d98c8b2-b7ea-42e8-b5e8-eb2f53d99e7c.png)|![Screenshot 2021-10-13 230045](https://user-images.githubusercontent.com/66735783/137204693-0d3f21fc-f0dc-4034-8153-fa85dd2d12d2.png)|

### Файлы конфигурации
[SW2.txt](https://github.com/wadepac/juniors/files/7325414/R-O2.txt)
[SW1.txt](https://github.com/wadepac/juniors/files/7325420/R-O1.txt)
[R1.txt](https://github.com/wadepac/juniors/files/7325422/R-1.txt)

