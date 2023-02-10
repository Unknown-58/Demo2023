# DEMO2023

## :books: [Задание](https://github.com/Unknown-58/Demo2023/blob/main/Doc/Demo2022.pdf)

## :books: [Вариант если у вас Cisco вместо Linux](https://github.com/vladimir-shalnev/DEMO2022)

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


### Устанавливаем необходимые пакеты !Обязательно установить стандартные системные утилиты!:
<p>Tcmdump</p>
<p>Openssh-server (Для северов)</p>
<p>Openssh-client (Для клиентов)</p>
После настраиваем связь машин по таблице

### ISP:
```debian
nano /etc/network/interface
```
``` debian
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
nano /etc/network/interface
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