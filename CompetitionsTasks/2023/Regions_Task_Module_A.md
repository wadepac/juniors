


# КОНКУРСНОЕ ЗАДАНИЕ КОМПЕТЕНЦИИ
## «СЕТЕВОЕ И СИСТЕМНОЕ АДМИНИСТРИРОВАНИЕ»



## Структура модулей конкурсного задания (инвариант)

В случае, если в тексте задания не указано иное, все пользовательские учетные записи должны иметь пароль P@ssw0rd. 
На маршрутизаторе R0 логин/пароль по умолчанию - vyos/toor 
На межсетевых экранах FW* логин/пароль по умолчанию - root/toor 
На компьютерах с Debian 10/11 логин/пароль по умолчанию - user/P@ssw0rd и root/toor
Все проверки работы клиентских технологий (сайтов, клиентских VPN подключений и т.п.) будут выполняться из под пользователя user соответствующих клиентских машин. Сайты будут проверяться через стандартный браузер клиентской ОС (для Windows - Edge, для Linux - Firefox).
При выполнении настоящего задания всегда нужно руководствоваться правилом наименьших привилегий. 
Консольный доступ к виртуальной машине провайдера ISP для участника не предполагается. Следите за тем, чтобы виртуальная машина ISP была включена в течение всего времени выполнения задания.

### Модуль А. (Настройка технических и программных средств информационно-коммуникационных систем) 

Топология

<img width="500" alt="Снимок экрана 2023-03-10 в 15 44 06" src="https://user-images.githubusercontent.com/66735783/224319254-b75195be-4f46-4093-ab06-68ef59efa2ae.png">

ЗАДАНИЕ
1. Настройте статичные IPv4-адреса, шлюз по умолчанию и описания на интерфейсах FW* и R0 согласно схеме адресации.
2. Настройте статичные IPv4-адреса и шлюз по умолчанию на всех устройствах, где это требуется, согласно схеме адресации.
3. [Настройте OSPFv2 между R0-MSK и FW-MSK](../../Networking/Routing/OSPF/README.md)
   - 1. FW-MSK должен узнавать о сетях LAN-MSK и SRV-MSK через OSPF. 
   - 2. R0 должен получать маршрут по умолчанию и другие необходимые маршруты от FW-MSK через OSPF. 
   - 3. Не используйте статические маршруты до этих сетей. Статические маршруты применимы только в качестве временной меры.
   - 4. R0-MSK должен быть защищен от вброса маршрутов с интерфейсов смотрящих в сторону сетей LAN-MSK и SRV-MSK 
   - 5. FW-MSK должен быть защищен от вброса маршрутов с интерфейса смотрящего в сторону сети DMZ-MSK.
4. [Обеспечьте выход в интернет для всех устройств московского и амстердамского офисов.](../../OperatingSystems/Firewall/nftables/README.md)
5. [Настройте сервер разрешения имен](../../OperatingSystems/DNS/README.md)
   - 1. Устройства в локальных сетях должны обращаться с DNS запросами к своим FW* 
   - 2. Пограничные маршрутизаторы FW* должны выполнять пересылку DNS запросов от локальных клиентов на DNS сервер по адресу 100.100.100.100.
   - 3. ClientSPB и VDS должны обращаться с DNS запросами к 100.100.100.100.
6. Настройте имена устройств согласно топологии.
7. [Настройте для всех устройств московского и амстердамского офисов доменные имена в зонах msk.jun39.wsr и ams.jun39.wsr соответственно.](../../OperatingSystems/DNS/README.md)
   - 1. Все устройства должны быть доступны в локальных сетях всех филиалов по именам в соответствии с топологией в доменах соответствующих филиалов. К примеру dmz-ams.ams.jun39.wsr или pc-msk.msk.jun39.wsr
   - 2. В рамках каждого филиала короткие имена должны автоматически дополняться доменным именем соответствующего филиала
8. [Настройте DHCP-сервер на SRV1–MSK для клиентов в сети LAN–MSK и DHCP-сервер на FW-AMS для сети LAN–AMS. DHCP-сервер должен передавать клиентам следующие опции:](../../OperatingSystems/DHCP/README.md)
   - 1. Адрес хоста
   - 2. Маску сети 
   - 3. Адрес шлюза
   - 4. Имя домена (msk.jun39.wsr и ams.jun39.wsr соответственно.)
   - 5. Адрес DNS (FW*)
   - 6. Адрес NTP (FW*)
   - 7. Выдаваемый диапазон адресов должен иметь запас в как минимум по 10 адресов в начале и конце сети, но не более 50 суммарного запаса. 
9. [Настройте DHCP Relay на маршрутизаторе R0-MSK таким образом, чтобы клиентам в сети LAN–MSK адреса выдавал сервер SRV1–MSK.](../../OperatingSystems/DHCP/README.md)
10. [Настройте синхронизацию времени](../../OperatingSystems/NTP/README.md)
   - 1. Устройства в локальных сетях должны синхронизировать свое время с FW*.
   - 2. Устройства с динамическими адресами должны получать получать информацию о сервере времени от своего DHCP сервера и использовать ее для работы.
   - 3. Пограничные маршрутизаторы FW*, ClientSPB и VDS должны синхронизировать свое время с NTP сервером по адресу 100.101.102.103.
   - 4. Настройте часовой пояс на всех устройствах в соответствии с их географическим расположением.
11. Настройте правила firewall так, чтобы устройства в сетях DMZ-* не могли инициировать соединения к клиентам в приватных сетях организации, при этом входящие соединения из всех локальных сетей в сети DMZ-* должны быть разрешены и машины в сети DMZ-* должны иметь доступ в интернет. При необходимости, допускается возможность штучно открывать дополнительные порты, необходимые для выполнения задания.
12. [Настройте сетевое обнаружение по протоколу LLDP на всех сетевых устройствах и серверах](../../Networking/05_CDP_LLDP/README.md)
13. [Настроить удаленный доступ к VDS и R0-MSK по SSH](../../OperatingSystems/SSH/README.md)
   - 1. Устройство PC-MSK при входе под пользователем user должно иметь доступ к VDS под пользователем user c использованием SSH ключей, без необходимости ввода пароля.
   - 2. Подключение к VDS с PC-MSK должно осуществляться по имени “VDS”
14. [Настройте защищенный VPN-туннель FW-AMS<=>FW-MSK со следующими параметрами:](../../OperatingSystems/VPN/)
   - 1. Технология VPN на ваш выбор: IPsec, OpenVPN, WireGuard.
   - 2. Используйте современные надежные протоколы шифрования AES и SHA-2
   - 3. Не допускается использование протоколов шифрования и аутентификации с длиной ключа/хеша менее 256 бит.
   - 4. Настройте маршрутизацию, NAT и межсетевой экран таким образом, чтобы трафик для другого офиса не натировался и не блокировался
15. [Настройте работу OSPF между FW*, чтобы устройства из московского офиса имели связанность с устройствами из амстердамского.](../../Networking/Routing/OSPF/README.md)
16. [Обеспечьте подключение клиента VPNClient к серверу VPN на FW-MSK.](../../OperatingSystems/VPN/)
   - 1. Технология VPN на ваш выбор: IPsec, OpenVPN, WireGuard.
   - 2. Клиент должен иметь доступ к серверам в сети SRV-MSK
   - 3. Соединение должно автоматически устанавливаться при включении компьютера или входе под пользователем user.
17. [Настройте централизованный сбор журналов syslog на SRV1-MSK.](../../OperatingSystems/Logs/README.md)
   - 1. Журналы должны храниться в файлах /opt/logs/[hostname], где hostname - это короткое или полное доменное имя машины, предоставившей соответствующие сообщения.
   - 2. PC-MSK должен записывать сообщения error и более важные.
   - 3. SRV*-MSK должны записывать сообщения warning и более важные. 
   - 4. FW должен записывать сообщения от служб ospf и имеющихся на устройстве служб туннелирования (ipsec, openvpn, wireguard и т.д) уровня не менее notice.


Таблица Адресации

| **Сеть ** | **Устройство ** | **Адрес/Маска ** | **Шлюз**                  |
|:---------:|:---------------:|:----------------:|:-------------------------:|
| INTERNET  | ClientEU        | 100.70.2.45/27   | ISP – первый адрес в сети |
|           | FW-AMS          | 100.70.3.45/26   | ISP – первый адрес в сети |
|           | FW-MSK          | 100.70.4.18/28   | ISP – первый адрес в сети |
|           | ClientSPB       | 100.70.5.55/28   | ISP – первый адрес в сети |
|           | VDS (EKB)       | 100.70.6.12/29   | ISP – первый адрес в сети |
|           | VPNClient (EKB) | 100.70.6.13/29   | ISP – первый адрес в сети |
|           | DNS-сервер      | 100.100.100.100  |                           |
|           | NTP-сервер      | 100.101.102.103  |                           |
| FW–R0–MSK | FW-MSK          | 192.168.1.1/30   |                           |
|           | R0-MSK          | 192.168.1.2/30   | 192.168.1.1               |
| LAN–MSK   | R0–MSK          | 192.168.2.1/24   |                           |
|           | PC–MSK          | DHCP             | 192.168.2.1               |
| SRV–MSK   | R0–MSK          | 192.168.3.1/24   |                           |
|           | SRV1–MSK        | 192.168.3.2/24   | 192.168.3.1               |
|           | SRV2–MSK        | 192.168.3.3/24   | 192.168.3.1               |
| DMZ–MSK   | FW–MSK          | 192.168.4.1/30   |                           |
|           | APP–MSK         | 192.168.4.2/30   | 192.168.4.1               |
| LAN–AMS   | FW–AMS          | 172.16.1.1/24    |                           |
|           | PC–AMS          | DHCP             | 172.16.1.1                |
| DMZ–AMS   | FW–AMS          | 172.16.2.1/24    |                           |
|           | DMZ–AMS         | 172.16.2.2/24    | 172.16.2.1                |
|           | APP–AMS         | 172.16.2.3/24    | 172.16.2.1                |

