## OpenSSL Certification Authority

### Установка и настройка корневого центра сертификации

1. Установка пакетов
```
sudo apt update
sudo apt install easy-rsa
```
2. Создадим корневую директорию
```
mkdir /opt/ca
```
3. Инициализируем наш центр сертифкации
```
cd ~/opt/ca
./easyrsa init-pki
```
4. Создадим файл с переменными vars
```
set_var EASYRSA_REQ_COUNTRY    "RU"
set_var EASYRSA_REQ_PROVINCE   "Moscow"
set_var EASYRSA_REQ_CITY       "Moscow"
set_var EASYRSA_REQ_ORG        "WSR"
set_var EASYRSA_REQ_EMAIL      "wsr@jun39.msk"
set_var EASYRSA_REQ_OU         "WSR CA"
set_var EASYRSA_ALGO           "ec"
set_var EASYRSA_DIGEST         "sha512" 
```
5.
6.
7.
8.