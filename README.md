# DEMO2023

### :books: [Задание](https://github.com/Unknown-58/Demo2023/blob/main/Doc/Demo2022.pdf)

### :books: [Вариант если у вас Cisco вместо Linux](https://github.com/vladimir-shalnev/DEMO2022)

# Вариант #1

### Виртуальные машины и коммутация.
Необходимо выполнить создание и базовую конфигурацию виртуальных
машин.
![image](https://github.com/Pavel58-pnz/Demo2022/blob/main/image/Demo.png)
1. На основе предоставленных VM или шаблонов VM создайте отсутствующие виртуальные машины в соответствии со схемой.  
   -	Характеристики VM установите в соответствии с Таблицей 1;
   -	Коммутацию (если таковая не выполнена) выполните в соответствии со схемой сети.	 
2.  Имена хостов в созданных VM должны быть установлены в соответствии со схемой.
3.  Адресация должна быть выполнена в соответствии с Таблицей 1;
4.  Обеспечьте VM дополнительными дисками, если таковое необходимо в соответствии с Таблицей 1;
## Таблица 1. Характеристики VM
|Name VM         |ОС                  |RAM             |CPU             |IP                    |Additionally                       |
|  ------------- | -------------      | -------------  |  ------------- |  -------------       |  -------------                    |  
|ISP             |Debian 11           |2 GB            |2               |3.3.3.1/24            |                                   |
|                |                    |                |                |4.4.4.1/24            |                                   |
|                |                    |                |                |5.5.5.1/24            |                                   |
|RTR-L           |Debian 11           |2 GB            |2/4             |4.4.4.100/24          |                                   |
|                |                    |                |                |192.168.101.254/24    |                                   |
|RTR-R           |Debian 11           |2 GB            |2/4             |5.5.5.100/24          |                                   |
|                |                    |                |                |172.16.101.254 /24    |                                   |
|SRV             |Debian 11/ Win 2019 |2 GB /4 GB      |2/4             |192.168.101.200/24    |Доп диски 2 шт по 5 GB             |
|WEB-L           |Debian 11           |2 GB            |2               |192.168.101.100/24    |                                   |
|WEB-R           |Debian 11           |2 GB            |2               |172.16.101.100/24     |                                   |
|CLI             |Win 10              |4 GB            |4               |3.3.3.10/24           |                                   |

### 1. На основе предоставленных ВМ или шаблонов ВМ создайте отсутствующие виртуальные машины в соответствии со схемой.
Убедитесь что все ВМ созданы в соотведствии со схемой 

![image](https://raw.githubusercontent.com/Unknown-58/Demo2023/main/image/Demo02.jpg)

## Изменение имена машин (Меняем {name} на всех машина: например (ISP, RTR-R, RTR-L т.д. )) 
```debian 
echo {name} > /etc/hostname
```
## Устанавливаем необходимые пакеты > Обязательно установить стандартные системные утилиты < :
### Удаляем всё что находится в `sourcses.list` и меняем на следующие репозитории:
```debian
nano /etc/apt/sourses.list
```
```debian
deb http://mirror.corbina.net/debian/ buster main
deb-src http://mirror.corbina.net/debian/ buster main

deb http://security.debian.org/debian-security buster/updates main
deb-src http://security.debian.org/debian-security buster/updates main

deb http://mirror.corbina.net/debian/ buster-updates main
deb-src http://mirror.corbina.net/debian/ buster-updates main
```
или
```debian
deb http://deb.debian.org/debian bullseye main contrib non-free
deb-src http://deb.debian.org/debian bullseye main contrib non-free

deb http://deb.debian.org/debian-security/ bullseye-security main contrib non-free
deb-src http://deb.debian.org/debian-security/ bullseye-security main contrib non-free

deb http://deb.debian.org/debian bullseye-updates main contrib non-free
deb-src http://deb.debian.org/debian bullseye-updates main contrib non-free
```
или
```debian
deb http://mirror.yandex.ru/debian bullseye main
deb-src http://mirror.yandex.ru/debian bullseye main

deb http://mirror.yandex.ru/debian bullseye-updates main
deb-src http://mirror.yandex.ru/debian bullseye-updates main

deb https://mirror.yandex.ru/debian-security bullseye-security main
deb-src https://mirror.yandex.ru/debian-security bullseye-security main
```
### Проверяет наличие обновлений для пакетов 
```debian 
apt-get update
```
А также обновляем их
```debian 
apt-get upgrade
``` 
### ISP:
```debian
apt install bind9 bind9utils dnsutils chrony openssh-server tcpdump -y
```
### RTR-R:
```debian
apt install openssh-server firewalld chrony tcpdump wireguard wireguard-tools -y
```
или (если настраиваете IPsec)
```debian
apt install openssh-server firewalld chrony tcpdump strongswan -y
```
### RTR-L:
```debian
apt install openssh-server firewalld chrony tcpdump wireguard wireguard-tools -y
```
или (если настраиваете IPsec)
```debian
apt install openssh-server firewalld chrony tcpdump strongswan -y
```
### WEB-L:
```debian
apt install host chrony openssh-server cifs-utils -y
```
### WEB-R:
```debian
apt install host chrony openssh-server cifs-utils -y
```

## После настраиваем сетевую связность машин
### Раскоментируем `net.ipv4.ip_forward=1` для всех машин:
```debian
nano /etc/sysctl.conf
```
Проверяем ipv4
```debian
sysctl -p
```
Должно показать
```
net.ipv4.ip_forward=1
```
Для проверки `ens{#}` используем:
```debian
ip a
```
### ISP:
```debian
nano /etc/network/interfaces
```
```debian
auto ens33
iface ens33 inet static
address 3.3.3.1
netmask 255.255.255.0

auto ens36
iface ens36 inet static
address 4.4.4.1
netmask 255.255.255.0

auto ens37
iface ens37 inet static
address 5.5.5.1
netmask 255.255.255.0
```
Провести перезагрузку сервиса на всех машинах после настройки:
```debian 
service networking restart
```
### RTR-L:
```debian
nano /etc/network/interfaces
```
```debian
auto ens33
iface ens33 inet static
address 4.4.4.100
netmask 255.255.255.0
gateway 4.4.4.1

auto ens36
iface ens36 inet static
address 192.168.101.254
netmask 255.255.255.0
```
### RTR-R:
```debian
nano /etc/network/interfaces
```
```debian
auto ens33
iface ens33 inet static
address 5.5.5.100
netmask 255.255.255.0
gateway 5.5.5.1

auto ens36
iface ens36 inet static
address 172.16.101.254
netmask 255.255.255.0
```
### WEB-L:
```debian
nano /etc/network/interfaces
```
```debian
auto ens33
iface ens33 inet static
address 192.168.101.100
netmask 255.255.255.0
gateway 192.168.101.254
```
### WEB-R:
```debian
nano /etc/network/interfaces
```
```debian
auto ens33
iface ens33 inet static
address 172.16.101.100
netmask 255.255.255.0
gateway 172.16.101.254
```
## Настройка `SSH`:
### Заходим на WEB-L:
```debian
nano /etc/ssh/sshd_config
```
Раскоментируем `PermitRootLogin` и пришем `yes`:
```debian
#Authentication:

#LoginGraceTime 2m
PermitRootLogin yes
#StrictModes yes
#MaxAuthTries 6
#MaxSessions 10
```
```debian
systemctl restart ssh
```
Проверяем работаспособность `SSH` пишем:
```debian
ssh localhost
```
Аналогично делаем на следующих машинах `RTR-L` `RTR-R` `WEB-R` 
## Настройка `Firewalld`:
### Заходим на `RTR-L` и проверяем активность зон:
```debian
firewall-cmd --get-active-zones
```
Посмотрим какие зоны мы можем настроить:
```debian
firewall-cmd --list-all-zones
```
Создаём зоны внутренею `trusted` и внешнию `external`:

В сторону ISP, смотрит `external`:
```debian
firewall-cmd --zone=trusted --add-interface=ens33
```
В сторону WEB-L, смотрит `trusted`:
```debian
firewall-cmd --zone=external --add-interface=ens36
```
Проверяем создались ли зоны:
```debian
firewall-cmd --get-active-zones
```
Разрешаем доступы для `http` `https` `DNS`:
```debian
firewall-cmd --zone=external --add-service=http
```
```debian
firewall-cmd --zone=external --add-service=https
```
```debian
firewall-cmd --zone=external --add-service=dns
```
Пробрасываем следующие порты `80` `22` `53` port-SSH: `2222` `2244` могу поменяться:
Пробрасываем порты `80` `22` `2222` на WEB-L для `http` `https` `SSH`:
```debian
firewall-cmd --zone=external --add-forward-port=port=2222:proto=tcp:toport=22:toaddr=192.168.101.100
```
IP-address может поменяться
Пробрасываем порт `53` на SRV для DNS:
```debian
firewall-cmd --zone=external --add-forward-port=port=53:proto=udp:toport=53:toaddr=192.168.101.200
```
И для VPN пробрасываем порт `123456`:
```debian
firewall-cmd --zone=external --add-port=12345/udp
```
Порт нужно поменять на свой
Проверяем настройки `firewalld`:
```debian
firewall-cmd --zone=external --list-all
```
Сохраняем настройки `firewalld`:
```debian
firewall-cmd --runtime-to-permanent
```
Перезагружаем `firewalld`:
```debian
firewall-cmd --reload
```
### Заходим на RTR-R и делаем аналогично:
```debian
firewall-cmd --get-active-zones
```
Посмотрим какие зоны мы можем настроить
```debian
firewall-cmd --list-all-zones
```
Создаём зоны внутренею `trusted` и внешнию `external`:

В сторону ISP, смотрит `external`:
```debian
firewall-cmd --zone=trusted --add-interface=ens33
```
В сторону WEB-R, смотрит `trusted`:
```debian
firewall-cmd --zone=external --add-interface=ens36
```
Проверяем создались ли зоны:
```debian
firewall-cmd --get-active-zones
```
Разрешаем доступы для `http` `https` `DNS`:
```debian
firewall-cmd --zone=external --add-service=http
```
```debian
firewall-cmd --zone=external --add-service=https
```
```debian
firewall-cmd --zone=external --add-service=dns
```
Пробрасываем следующие порты `80` `22` `53` port-SSH: `2222` `2244` могу поменяться:
Пробрасываем порты `80` `22` `2244` на WEB-R для `http` `https` `SSH`:
```debian
firewall-cmd --zone=external --add-forward-port=port=2244:proto=tcp:toport=22:toaddr=172.16.101.100
```
IP-address может поменяться
Пробрасывать порт `53` на `RTR-R` для DNS не нужно:
И для VPN пробрасываем порт `123456`:
```debian
firewall-cmd --zone=external --add-port=12345/udp
```
Порт нужно поменять на свой
Проверяем настройки `firewalld`:
```debian
firewall-cmd --zone=external --list-all
```
Сохраняем настройки `firewalld`:
```debian
firewall-cmd --runtime-to-permanent
```
Перезагружаем `firewalld`:
```debian
firewall-cmd --reload
```
## Настройка `GRE-tunnel`:
### RTR-R GRE-tunnel:
```debian
nano /etc/network/interfaces
```
```debian
auto ens33
iface ens33 inet static
address 5.5.5.100
netmask 255.255.255.0
gateway 5.5.5.1

#GRE-tunnel
auto gre1
iface gre1 inet static
address 1.1.1.2
netmask 255.255.255.252
mode gre
local 5.5.5.100 
endpoint 4.4.4.100
pre-up ip tunnel add gre1 mode gre remote 4.4.4.100 local 5.5.5.100
post-up ip route add 192.168.101.0/24 via 10.10.10.1 dev gre1

auto ens36
iface ens36 inet static
address 172.16.101.254
netmask 255.255.255.0
```
### RTR-L GRE-tunnel:
```debian
nano /etc/network/interfaces
```
```debian
auto ens33
iface ens33 inet static
address 4.4.4.100
netmask 255.255.255.0
gateway 4.4.4.1

#GRE-tunnel
auto gre1
iface gre1 inet static
address 1.1.1.1
netmask 255.255.255.252
mode gre
local 4.4.4.100
endpoint 5.5.5.100
pre-up ip tunnel add gre1 mode gre remote 5.5.5.100 local 4.4.4.100
post-up ip route add 172.16.101.0/24 via 10.10.10.2 dev gre1

auto ens36
iface ens36 inet static
address 192.168.101.254
netmask 255.255.255.0
```
## Настройка `Wireguard`:
### Заходим в `RTR-L`:
Создаём папку для ключей:
```debian
mkdir /etc/wireguard/keys
```
```debian
cd /etc/wireguard/keys
```
```debian
ls
```
Создаём ключ для `SRV`:
```debian
wg genkey | tee srv-sec.key | wg pubkey > srv-pub.key
```
Проверяем появились ли ключи:
```debian
ls -l
```
Создаём ключ для `CLI`:
```debian
wg genkey | tee cli-sec.key | wg pubkey > cli-pub.key
```
Проверяем появились ли ключи:
```debian
ls -l
```
Прописываем ключи в конфиг:
```debian
cat srv-sec.key cli-pub.key >> /etc/wireguard/wg0.conf
```
Заходим и редактируем этот файл `/etc/wireguard/wg0.conf`:
```debian
[Interface]
Address = 1.1.1.1/30
ListPort = 12345
PrivateKey = cdexswzaqQAZWSXEDC123456789 

[Peer]
PublicKey = 123456789QAZWSXEDCcdexswzaq
AllowedIPs = 1.1.1.0/30, 172.16.1.0/24
```
- Там где `Address` - ставим любой IP-address например: `1.1.1.1/30`.
- Там где `ListPort` - ставим наш `123456`.
- Там где `AllowedIPs` - должно быть IP-address `1.1.1.0/30` и наше посети `172.16.101.0/24`.
- Там где `PrivateKey` - первый созданный ключ.
- Там где `PubliKey` - второй созданный ключ.
#### Теперь сравниваем:
```debian
cat /etc/wireguard/wg0.conf
```
```debian
cat srv-sec.key
```
```debian
cat cli-pub.key
```
Запускаем сервис:
```debian
systemctl enable --now wg-quick@wg0
```
Проверяем:
```debian
systemctl status wg-quick@wg0
```
```debian
wg show all
```
```debian
ip route
```
### Далее переходим на `RTR-R`:
Создаём папку для ключей:
```debian
mkdir /etc/wireguard/keys
```
Переходим `RTR-L`:
```debian
scp cli-sec.key srv-pub.key 5.5.5.100:/etc/wireguard/keys
```
Провереяем на `RTR-R` ключи:
```debian
ls -l /etc/wireguard/keys
```
Прописываем ключи в конфиг:
```debian
cat cli-sec.key srv-pub.key >> /etc/wireguard/wg0.conf
```
Заходим и редактируем этот файл `/etc/wireguard/wg0.conf`:
```debian
[Interface]
Address = 1.1.1.2/30
PrivateKey = Сюда первый ключ

[Peer]
PublicKey = Сюда второй ключ
Endpoint = 4.4.4.100:12345
AllowedIPs = 1.1.1.0/30, 192.168.1.0/24
PersistentKeeplive = 10
```
- Там где `Address` - ставим любой IP-address например: `1.1.1.2/30`.
- Там где `Endpoint` - ставим наш `4.4.4.100:123456`.
- Там где `AllowedIPs` - должно быть IP-address `1.1.1.0/30` и наше посети `192.168.101.0/24`.
- Там где `PrivateKey` - первый созданый ключ.
- Там где `PubliKey` - второй созданый ключ.
#### Теперь сравниваем:
```debian
cat /etc/wireguard/wg0.conf
```
```debian
cat srv-sec.key
```
```debian
cat cli-pub.key
```
Запускаем сервис:
```debian
systemctl enable --now wg-quick@wg0
```
Проверяем:
```debian
systemctl status wg-quick@wg0
```
```debian
wg show all
```
```debian
ip route
```
## Настройка `DNS`:
Заходим на всех машинах `resolv.conf`:
`ISP`:
```debian
domain demo.wsr
search demo.wsr
nameserver 3.3.3.1
```
`CLI`:
![image](https://github.com/Unknown-58/Demo2023/blob/main/image/1.png)
`SRV`:
![image](https://github.com/Unknown-58/Demo2023/blob/main/image/2.png)
`RTR-L`:
```debian
domain int.demo.wsr
search int.demo.wsr
nameserver 192.168.100.200
```
`RTR-R`:
```debian
domain int.demo.wsr
search int.demo.wsr
nameserver 4.4.4.100
```
`WEB-L`:
```debian
domain int.demo.wsr
search int.demo.wsr
nameserver 192.168.100.200
```
`WEB-R`:
```debian
domain int.demo.wsr
search int.demo.wsr
nameserver 4.4.4.100
```
## Настройка `IPSEC`:
### Заходим в `ipsec.conf` на обоих роутерах `RTR-L`: 
```debian
nano /etc/ipsec.conf
```
```debian
config setup
   charondebug="all"
   uniqueids=yes

conn %default
   ikelifetime=60m
   keylife=20m
   rekeymargin=3m
   keyingtries=1

conn vpn-ipsec
   auto=start
   type=tunnel
   authby=secret
   left=4.4.4.100
   leftsubnet=192.168.101.0/24
   right=5.5.5.100
   rightsubnet=172.16.101.0/24
   keyexchange=ikev1
```
### RTR-R:
```debian
nano /etc/ipsec.conf
```
```debian
config setup
   charondebug="all"
   uniqueids=yes

conn %default
   ikelifetime=60m
   keylife=20m
   rekeymargin=3m
   keyingtries=1

conn vpn-ipsec
   auto=start
   type=tunnel
   authby=secret
   left=5.5.5.100
   leftsubnet=172.16.101.0/24
   right=4.4.4.100
   rightsubnet=192.168.101.0/24
   keyexchange=ikev1
```
## Прописываем IPsec.secrets на обоих роутерах `RTR-L` и `RTR-R`:
```debian
nano /etc/ipsec.secrets
```
```debian
4.4.4.100 5.5.5.100 : PSK "qwertyuiop123456789"
```
Запускаем `ipsec` или перезагружаем:
```debian
ipsec start
```
или
```debian
ipsec restart
```
Проверяем в `ISP` через `tcpdump`:
```debian
tcpdump | grep ESP
```
