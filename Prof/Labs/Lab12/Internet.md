#  Основные протоколы сети интернет.

###  Задание:

Настроить DHCP в офисе Москва
Настроить синхронизацию времени в офисе Москва
Настроить NAT в офисе Москва, C.-Перетбруг и Чокурдах


1. Настроите NAT(PAT) на R14 и R15. Трансляция должна осуществляться в адрес автономной системы AS1001
2. Настроите NAT(PAT) на R18. Трансляция должна осуществляться в пул из 5 адресов автономной системы AS2042
3. Настроите статический NAT для R20
4. Настроите NAT так, чтобы R19 был доступен с любого узла для удаленного управления
5. Настроите статический NAT(PAT) для офиса Чокурдах.
6. Настроите для IPv4 DHCP сервер в офисе Москва на маршрутизаторах R12 и R13. VPC1 и VPC7 должны получать сетевые настройки по DHCP
7. Настроите NTP сервер на R12 и R13. Все устройства в офисе Москва должны синхронизировать время с R12 и R13

![image](https://github.com/user-attachments/assets/04707f54-78e8-4f23-9ebe-14210cb62d46)

## 1. Настроите NAT(PAT) на R14 и R15. Трансляция должна осуществляться в адрес автономной системы AS1001.

1. На роутерах R14 и R15 создадим access-list для подсетей офиса Москва (192.168.20.0 /24 и 192.168.30.0 /24)

       access-list 1 permit 192.168.0.0 0.0.255.255
       
2. На роутерах R14 и R15: 

   назначим интерфейсы e0/0 как ip nat inside

   назначим интерфейсы e0/2 как ip nat outside

3. На роутерах R14 и R15 создадим запись для Nat трансляции согласно задания

       ip nat inside source list 1 interface Loopback1 overload
       
    Loopback1 это PI адреса AS1001

    для R14 LoppBack1 100.100.100.14 /32

    для R15 LoppBack1 100.100.100.15 /32
    
    Пинг из офиса Москвы с ПК (192.168.20.4) на роутер С-Петербург R18 на интерфейсе которого настроен белый IP из диапозона PI адресов AS2042 Питер (200.200.30.18 /32)

![image](https://github.com/user-attachments/assets/3390e793-d653-4bcf-a646-5b519ea14093)

На роутере R15 появилась запись в NAT трансляции

![image](https://github.com/user-attachments/assets/5d8f7664-9354-4402-b8ad-b375843e921b)

Выход в Интернет осуществляется через роутер R15 согласно предыдущих заданий. Для проверки R14, отключим R15 и убедимся, что NAT рансляция так же проходит через R14

![R14 NAT RT](https://github.com/user-attachments/assets/617c9183-7dda-49e9-9e92-a6eb14df2500)



## 2. Настроите NAT(PAT) на R18. Трансляция должна осуществляться в пул из 5 адресов автономной системы AS2042

1. На роутере R18 Питер создадим access-list для подсетей офиса Питер (192.168.40.0 /24 и 192.168.50.0 /24)

       access-list 1 permit 192.168.0.0 0.0.255.255
2. Создадим LoopBack2 с IP адресом 200.200.30.1 /29. Данная сеть включает 6 IP, что удовлетворяет услвоию задания.

       interface Loopback2
       ip address 200.200.30.1 255.255.255.248
3. Создадим Pool для 5 NAT адресов

       ip nat pool AS_2042 200.200.30.2 200.200.30.6 netmask 255.255.255.248

4. Создадим NAT трансляицю

       ip nat inside source list 1 pool AS_2042 overload

5. На роутере R18:

назначим интерфейсы e0/0 и e0/1 как ip nat inside

назначим интерфейсы e0/2 и e0/3 как ip nat outside
   
Пропингуем" с офиса Питера с ПК (192.168.40.2) роутер Москвы R15 на интерфейсе которого настроен белый IP из диапозона PI адресов AS1001 Москва (100.100.20.15 /32)

![изображение](https://github.com/user-attachments/assets/7c25ff34-e3a4-4cec-bc54-84ba0d91002a)

NAT трансляция на роутере R18 C-Петербург

![изображение](https://github.com/user-attachments/assets/662aab92-040d-4446-912b-45c4d48a94fe)

## 3.Настроите статический NAT для R20

Введем в схему еще одну подсеть офиса 192.168.80.0 /24, подключим ее на интерфейс роутера R20.

       interface Ethernet0/1
       ip address 192.168.80.1 255.255.255.0
       ip nat inside

На интерфейсе e0/0 роутера R20 добавим второй IP адрес (secondary). Это необходимо для того, что бы NAT трансляция осуществлялась через IP secondary адрес. Если выполнить трансляцию через основной адрес, который учавствует в OSPF, то это вызовет проблемы с соседством по OSPF

       interface Ethernet0/0
       ip address 20.20.20.41 255.255.255.252 secondary
       ip address 20.20.20.38 255.255.255.252
       ip nat outside

На R20 cоздадим статическую NAT трансляицю

       ip nat inside source static 192.168.80.2 20.20.20.41

Для того, что бы из подсети 192.168.80.0 можно было обращаться в интернет, нужно выполнить дополнительную настройку на роутере R15, а именно:

создать еще один access-list для подсети в которую входит secondary адрес роутера R20

       access-list 2 permit 20.20.20.40 0.0.0.3

создать NAT трансляцию для этого access-list

       ip nat inside source list 2 interface Loopback1 overload 

Интерфейс e0/3 роутера R15 сделать как ip nat inside

![изображение](https://github.com/user-attachments/assets/41d1087d-e5b6-4f78-b10a-f4befa59a426)

![изображение](https://github.com/user-attachments/assets/02c36b5a-c9db-4e4f-8718-3fd32463a587)

![изображение](https://github.com/user-attachments/assets/4e346a93-f8b6-4890-a350-a6f586b6ab57)


## 4. Настроите NAT так, чтобы R19 был доступен с любого узла для удаленного управления

Для выполнения это задания необходимо на роутере R14 прописать команду перенаправления портов

        ip nat inside source static tcp 19.19.19.19 22 100.100.20.14 22 extendable

В качестве Local адреса использован Loopback интерфейс 19.19.19.19

Далее необходимо так же на роутере R14 прописать интерфейс e0/3 смотрящий на R19 как ip nat inside

       interface Ethernet0/3
       ip nat inside
и интерфейс e0/1 смотрящий на R15 как ip nat outside. 

       interface Ethernet0/1
       ip nat outside


## 5.Настроите статический NAT(PAT) для офиса Чокурдах

 Сети офиса Чокурдах выходят через один канал, по умолчанию через R25 130.130.130.1. Если канал падает, то будет происходить переключение на R26 140.140.140.1. Для этого понадобится трек отслеживания. IP Sla пингует хост 130.130.130.1 кажые 5 сек. И если хост 130.130.130.1 падает то на R28 Чокурадх появляется следущее сообщение

        "%TRACKING-5-STATE: 1 ip sla 1 reachability Up->Down"

Если хост 130.130.130.1 поднимается, то появляется следущее сообщение

       "%TRACKING-5-STATE: 1 ip sla 1 reachability Down->Up"

На R28 в таблице маршрутизации находятся два маршрута по умолчанию

       ip route 0.0.0.0 0.0.0.0 130.130.130.1 track 1
       ip route 0.0.0.0 0.0.0.0 140.140.140.1 20

На R28 пометим интерфейсы как IP Nat Outside и IP Nat Inside
       
       e0/0 и e0/1 - ip nat outside
       e0/2.60 и e0/2.70 - ip nat inside

Создадим access-list для подсетей офиса Чокурдах

       access-list 10 permit 192.168.0.0 0.0.255.255

На интерфейс e0/1 R28 добавим secondary адрес. Из под этого адреса будет осуществляться nat трансляция. Если выполнять nat трансляцию через основной ip, то трек отслеживания перестанет работать.

       interface Ethernet0/1
       ip address 130.130.130.3 255.255.255.240 secondary
       ip address 130.130.130.2 255.255.255.240
       ip nat outside

На R28 создадим два пула NAT. Это необходимо для переключения между пулами, когда падает основной линк.

       ip nat pool NAT_130 130.130.130.3 130.130.130.3 netmask 255.255.255.240
       ip nat pool NAT_140 140.140.140.2 140.140.140.2 netmask 255.255.255.240

Создадим Nat трансляцию которая будет использоваться по умолчанию

       ip nat inside source list 10 pool NAT_130 overload   

Настроим Cisco Event Менеджер

       event manager applet NAT_130_DOWN - название Эвента
       event syslog pattern "%TRACKING-5-STATE: 1 ip sla 1 reachability Up->Down" - событие по которому будет происходить выполнение последовательных команд
       action 1.1 cli command "enable"
       action 1.2 cli command "clear ip nat tr *"
       action 1.3 cli command "configure terminal"
       action 1.4 cli command "no ip nat inside source list 10 pool NAT_130 overload"
       action 1.5 cli command "ip nat inside source list 10 pool NAT_140 overload"
       action 1.6 cli command "end"
       event manager applet NAT_130_UP
       event syslog pattern "%TRACKING-5-STATE: 1 ip sla 1 reachability Down->Up"
       action 1.1  cli command "enable"
       action 1.2  cli command "clear ip nat tr *"
       action 1.3  cli command "configure terminal"
       action 1.4  cli command "no ip nat inside source list 10 pool NAT_140 overload"
       action 1.5  cli command "ip nat inside source list 10 pool NAT_130 overload"
       action 1.6  cli command "end"

при срабатывании срабатывает %TRACKING-5-STATE: 1 ip sla 1 reachability Up->Down и происходит переключение с маршрута по умолчанию на 140.140.140.1 и меняется трансляцию серых адресов из пула NAT_130 в NAT_140
![изображение](https://github.com/user-attachments/assets/fa87671b-e51d-422f-b62e-e70ade8d765d)

Так же когда срабатывает TRACKING-5-STATE: 1 ip sla 1 reachability Down->Up, то происходит обратная ситуация и так же меняется маршрут по умолчанию и трансляцию адресов в другой пул


