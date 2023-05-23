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
### Удаляем всё что находится в `source.list` и меняем на следующие репозитории:
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
apt install openssh-server firewalld chrony tcpdump ipsec -y
```
### RTR-L:
```debian
apt install openssh-server firewalld chrony tcpdump wireguard wireguard-tools -y
```
или (если настраиваете IPsec)
```debian
apt install openssh-server firewalld chrony tcpdump ipsec -y
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
Для проверки `ens{#}` используем
```debian
ip a
```
### ISP:
```debian
nano /etc/network/interfaces
```
```debian
auto ens{#}
iface ens{#} inet static
address 3.3.3.1
netmask 255.255.255.0

auto ens{#}
iface ens{#} inet static
address 4.4.4.1
netmask 255.255.255.0

auto ens{#}
iface ens{#} inet static
address 5.5.5.1
netmask 255.255.255.0
```
### RTR-L:
```debian
nano /etc/network/interfaces
```
```debian
auto ens{#}
iface ens{#} inet static
address 4.4.4.100
netmask 255.255.255.0
gateway 4.4.4.1

auto ens{#}
iface ens{#} inet static
address 192.168.101.254
netmask 255.255.255.0
```
### RTR-R:
```debian
nano /etc/network/interface
```
```debian
auto ens{#}
iface ens{#} inet static
address 5.5.5.100
netmask 255.255.255.0
gateway 5.5.5.1

auto ens{#}
iface ens{#} inet static
address 172.16.101.254
netmask 255.255.255.0
```
### WEB-L:
```debian
nano /etc/network/interfaces
```
```debian
auto ens{#}
iface ens{#} inet static
address 192.168.101.100
netmask 255.255.255.0
gateway 192.168.101.254
```
### WEB-R:
```debian
nano /etc/network/interfaces
```
```debian
auto ens{#}
iface ens{#} inet static
address 172.16.101.100
netmask 255.255.255.0
gateway 172.16.101.254
```
## Настройка SSH:
### Заходим на WEB-L:
```debian
nano /etc/ssh/sshd_config
```
Раскоментируем `PermitRootLogin` и пришем `yes`:
```debian
#LoginGraceTime 2m
PermitRootLogin yes
#StrictModes yes
```
```debian
systemctl restart ssh
```
Аналогично делаем на следующих машинах `RTR-L` `RTR-R` `WEB-R` 
## Настройка `Firewalld`:
Заходим `RTR-L` и проверяем активность зон
```debian
firewalld-cmd --get-active-zones
```
Посмотрим какие зоны мы можем настроить
```debian
firewalld-cmd --list-all-zones
```
Создаём зоны внутренею `trusted` и внешнию `external`

`trusted`
```debian
firewalld-cmd --zone=trusted --add-interface=ens{ISP}
```
`external`
```debian
firewalld-cmd --zone=external --add-interface=ens{WEB-L}
```
Проверяем создались ли зоны:
```debian
firewalld-cmd --get-active-zones
```
Разрешаем доступы для `http` `https` `DNS`:
```debian
firewalld-cmd --zone=external --add-service=http
```
```debian
firewalld-cmd --zone=external --add-service=https
```
```debian
firewalld-cmd --zone=external --add-service=dns
```
Пробрасываем следующие порты `80` `443` `22` `53` port-SSH: `2222` `2244` могу поменяться:
Пробрасываем порт для WEB-L `80` `22` `2222`
```debian
firewalld-cmd --zone=external --add-for=http
```
## Настройка `GRE-Tunnel`:
### RTR-R GRE-tunnel:
```debian
nano /etc/network/interfaces
```
```debian
auto ens{#}
iface ens{#} inet static
address 5.5.5.100
netmask 255.255.255.0
gateway 5.5.5.1

#GRE-tunnel
auto gre1
iface gre1 inet static
address 10.10.10.2
netmask 255.255.255.252
mode gre
local 5.5.5.100 
endpoint 4.4.4.100
post-up ip route add 192.168.101.0/24 via 10.10.10.1 dev gre1

auto ens{#}
iface ens{#} inet static
address 172.16.101.254
netmask 255.255.255.0
```
### RTR-L Gre-tunnel:
```debian
nano /etc/network/interfaces
```
```debian
auto ens{#}
iface ens{#} inet static
address 4.4.4.100
netmask 255.255.255.0
gateway 4.4.4.1

#GRE-tunnel
auto gre1
iface gre1 inet static
address 10.10.10.1
netmask 255.255.255.252
mode gre
local 4.4.4.100
endpoint 5.5.5.100
post-up ip route add 172.16.101.0/24 via 10.10.10.2 dev gre1

auto ens{#}
iface ens{#} inet static
address 192.168.101.254
netmask 255.255.255.0
```
## Создаём IPsec.conf на обоих роутерах RTR-L и RTR-R
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

conn gre-tunnel
   left=4.4.4.100
   leftsubnet=192.168.101.0/24
   leftid=10.10.10.1
   right=5.5.5.100
   rightsubnet=172.16.101.0/24
   rightid=10.10.10.2
   keyexchange=ikev1
   auto=start
   type=tunnel
```
## Создаём IPsec.secrets на обоих роутерах RTR-L и RTR-R
```debian
nano /etc/ipsec.secrets
```
```debian
4.4.4.100 5.5.5.100 : PSK "<Создаём любой ключ.Главное больше 12 символов верхних и нижних регистров.>"
```
