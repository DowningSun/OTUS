# VPN. GRE. DmVPN

#### Цель:

Настроить GRE между офисами Москва и С.-Петербург

Настроить DMVPN между офисами Москва и Чокурдах, Лабытнанги


## 1  Настроите GRE между офисами Москва и С.-Петербург.

На роутере R15 создать интерфейс Tunnel 0 и выполнить настройки для создания GRE туннеля с С.-Петербург R18

    interface Tunnel0
  
    ip address 172.16.0.1 255.255.255.252 - подсеть для туннеля
  
    ip mtu 1400
  
    ip tcp adjust-mss 1360
  
    tunnel source 100.100.20.15 - PI адрес AS1001
  
    tunnel destination 200.200.30.18 - PI адрес AS2042

На роутерах Москвы R15 и C.-Петербурга R18 прописать статические маршруты до сетей офиса

    R15 ip route 192.168.40.0 255.255.255.0 Tunnel0
  
    R15 ip route 192.168.50.0 255.255.255.0 Tunnel0

    R18 ip route 192.168.20.0 255.255.255.0 Tunnel0
  
    R18 ip route 192.168.30.0 255.255.255.0 Tunnel0

После того, как интерфейсы на обоих концах перейдут в состояние UP выполним тпинг-тест.

![image](https://github.com/user-attachments/assets/7e229170-aa2b-4447-b530-9dfde66530ea)

как можно увидеть, трафик идёт через тунель.

Теперь стоит осздать канал между R14 и R18 на случай обрыва тунеля от R15

На роутере R14 создать интерфейс Tunnel 0 и выполнить настройки для создания GRE туннеля с Питером R18

    interface Tunnel0
    ip mtu 1400
    ip tcp adjust-mss 1360
    tunnel source 100.100.20.14 - PI адрес AS1001
    tunnel destination 200.200.30.18 - PI адрес AS2042

На роутере R18 создать интерфейс Tunnel 1 (это второй туннель на этом роутере) и выполнить настройки для создания GRE туннеля с Москвой R14

    interface Tunnel1
    ip address 172.16.0.6 255.255.255.252
    ip mtu 1400
    ip tcp adjust-mss 1360
    tunnel source 200.200.30.18 - PI адрес AS2042 
    tunnel destination 100.100.20.14 - PI адрес AS1001 

На роутерах R14 и R18 прописать статические маршруты до сетей офиса.

    R14 ip route 192.168.40.0 255.255.255.0 Tunnel0
    
    R14 ip route 192.168.50.0 255.255.255.0 Tunnel0

Для тунелей С.-Петербург-Москва R14 повышаем AD, т.к. уже есть маршруты до R15

    R18 ip route 192.168.20.0 255.255.255.0 Tunnel1 10

    R18 ip route 192.168.30.0 255.255.255.0 Tunnel1 10

Теперь на роутере R18 по два маршрута в каждую сеть, но в таблицу маршрутизации попадет маршрут с лучшей метрикой.

![image](https://github.com/user-attachments/assets/67c5da8e-d5d4-4d55-8ba1-7bd20229d591)

Команда "keepalive 10" прописанная ранее на R18 в конфиге Tunnel0 необходима для того, что бы каждые 10 секунд посылать ICMP запрос на R15. Если запрос "теряется", то интерфейс переходит в статус DOWN.


## 2. Настроите DMVPN между Москва и Чокурдах, Лабытнанги

На R15 Москва создадим Tunnel1. Подсесть для DMVPN будет 172.16.10.0 /24

    interface Tunnel1
    ip address 172.16.10.1 255.255.255.0 
    ip mtu 1400
    ip nhrp map multicast dynamic
    ip nhrp network-id 1 - задает идентификатор процесса NHRP
    ip nhrp redirect - данная команда включает режим 3 фазы DMVPN на HUB
    ip tcp adjust-mss 1360
    tunnel source Loopback1 - это PI адрес R15.
    tunnel mode gre multipoint
    ip ospf network point-to-multipoint - режим работы OSPF в NBMA сети
    ip ospf 1 area 0

На R27 Лабытнаги создадим Tunnel0

    interface Tunnel0
    ip address 172.16.10.2 255.255.255.0
    ip mtu 1400
    ip nhrp map 172.16.10.1 100.100.20.15 - указывает соответсвие между адресом туннеля и белым адресом Hub
    ip nhrp map multicast 100.100.20.15 
    ip nhrp network-id 1
    ip nhrp nhs 172.16.10.1
    ip nhrp shortcut - данная команда включает режим 3 фазы DMVPN на SPOKE
    ip tcp adjust-mss 1360
    tunnel source Ethernet0/0 - интерфейс источника. 
    tunnel mode gre multipoint - задает режим работы туннеля
    ip ospf network point-to-multipoint
    ip ospf 1 area 0

Строим дополнительный туннель на R14

    interface Tunnel1
    ip address 172.16.10.20 255.255.255.0 адрес подсети для туннеля DMVPN (из той же подсети, что и R15 у которого 172.16.10.1/24)
    ip mtu 1400
    ip nhrp map multicast dynamic
    ip nhrp network-id 1 - задает идентификатор процесса NHRP
    ip nhrp redirect - данная команда включает режим 3 фазы DMVPN на HUB
    ip tcp adjust-mss 1360
    tunnel source Loopback1 - это PI адрес R14. Можно было указать IP как в варианте с GRE (100.100.20.15)
    tunnel mode gre multipoint
    ip ospf network point-to-multipoint - режим работы OSPF в NBMA сети 
    ip ospf 1 area 0
