# Лабораторная работа 1. Базовая настройка коммутатора
##       Задачи
Часть 1. Проверка конфигурации коммутатора по умолчанию
Часть 2. Создание сети и настройка основных параметров устройства
    • Настройте базовые параметры коммутатора.
    • Настройте IP-адрес для ПК.
Часть 3. Проверка сетевых подключений
    • Отобразите конфигурацию устройства.
    • Протестируйте сквозное соединение, отправив эхо-запрос.
    • Протестируйте возможности удаленного управления с помощью Telnet.

##   Часть1. Создание сети и проверка настроек коммутатора по умолчанию
1. Создайте сеть согласно топологии.
   
![](https://otuslabs.ucoz.net/topologija.jpg)

 Установите консольное подключение к коммутатору с компьютера PC-A
 
![](https://otuslabs.ucoz.net/CPT_RS_232.jpg)

Почему нужно использовать консольное подключение для первоначальной настройки коммутатора? Почему нельзя подключиться к коммутатору через Telnet или SSH?
..*Потому, что изначально консольный порт является единственным возможным способом соединения с оборудованием, остальные не настроены.

2. Проверьте настройки коммутатора по умолчанию.
На данном этапе вам нужно проверить такие параметры коммутатора по умолчанию, как текущие настройки коммутатора, данные IOS, свойства интерфейса, сведения о VLAN и флеш-память.
..*Изучите текущий файл running configuration.
Для просмотра runing configuration исполдьзуем из привелигированного режима команду sh run

![]( https://otuslabs.ucoz.net/sh_run1.jpg)
![](https://otuslabs.ucoz.net/sh_run2.jpg)

Вопросы:
Сколько интерфейсов FastEthernet имеется на коммутаторе 2960?   **Согласно полученному отчету: 24 порта**
Сколько интерфейсов Gigabit Ethernet имеется на коммутаторе 2960? **Согласно полученному отчету: 2 порта**
Каков диапазон значений, отображаемых в vty-линиях? **Согласно полученному отчету: 0-15**
..*Изучите файл загрузочной конфигурации (startup configuration), который содержится в энергонезависимом ОЗУ (NVRAM).

![](https://otuslabs.ucoz.net/sh_start.jpg)

Вопрос:
Почему появляется это сообщение? **Сообщение startup-config is not present говорит о том, что на устройстве отсутствуют конфигурации, отличный от заводсткой.**

..*Изучите характеристики SVI для VLAN 1.

![](https://otuslabs.ucoz.net/sh_vlan1.jpg)

Вопросы:
Назначен ли IP-адрес сети VLAN 1? **Адрес интерфейсу не назначен**
Какой MAC-адрес имеет SVI? Возможны различные варианты ответов.  **Согласно получяенному отчету адрес 0001.c7bc.c5c6**
Данный интерфейс включен?  **Нет/ Vlan1 is administratively down, line protocol is down**

..* Изучите IP-свойства интерфейса SVI сети VLAN 1.

Вопрос:
Какие выходные данные вы видите?
sh ip int vlan1
Vlan1 is administratively down, line protocol is down
  Internet protocol processing disabled

..*Подсоедините кабель Ethernet компьютера PC-A к порту 6 на коммутаторе и изучите IP-свойства интерфейса SVI сети VLAN 1. Дождитесь согласования параметров скорости и дуплекса между коммутатором и ПК.
Вопрос:
Какие выходные данные вы видите? **при подключении кабеля к порту в консоли появилось сообщение об активации порта %LINK-5-CHANGED: Interface FastEthernet0/6, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/6, changed state to up**

..*Изучите сведения о версии ОС Cisco IOS на коммутаторе.
Вопросы:
Под управлением какой версии ОС Cisco IOS работает коммутатор? **Version 15.0(2)SE4**

Как называется файл образа системы? **System image file is "flash:c2960-lanbasek9-mz.150-2.SE4.bin"**

Какой базовый MAC-адрес назначен коммутатору? **Base ethernet MAC Address : 00:01:C7:BC:C5:C6**

..* Изучите свойства по умолчанию интерфейса FastEthernet, который используется компьютером PC-A.

    ![](https://otuslabs.ucoz.net/sh_int_fa06.jpg)
Вопрос:
Интерфейс включен или выключен? **FastEthernet0/6 is up сообщает нам о том, что интерфейс включен**
Что нужно сделать, чтобы включить интерфейс? ** в данный момент интерфейс включен, в ином случае потревбовалось бы ввести следующий набор  команд
Switch#conf t
Enter configuration commands, one per line. End with CNTL/Z.
Switch(config)#int fa 0/6
Switch(config-if)#no shutdown**

Какой MAC-адрес у интерфейса? **address is 0002.4a56.6e06**

Какие настройки скорости и дуплекса заданы в интерфейсе? ** из отчета sh int fa 0/6 Full-duplex, 100Mb/s**


..* Изучите параметры сети VLAN по умолчанию на коммутаторе.

![](https://otuslabs.ucoz.net/sh_VLANs.jpg)

Какое имя присвоено сети VLAN 1 по умолчанию?  **default**
Какие порты расположены в сети VLAN 1?  **Fa0/1-24 Gig0/1-2**
Активна ли сеть VLAN 1? **active**
К какому типу сетей VLAN принадлежит VLAN по умолчанию? **enet**

..* Изучите флеш-память.
Выполните одну из следующих команд, чтобы изучить содержимое флеш-каталога.
Switch# show flash 
Switch# dir flash:

![](https://otuslabs.ucoz.net/sh_flash.jpg)

В конце имени файла указано расширение, например .bin. Каталоги не имеют расширения файла.
Вопрос:
Какое имя присвоено образу Cisco IOS?   **2960-lanbasek9-mz.150-2.SE4.bin**


       ##   Часть 2. Настройка базовых параметров сетевых устройств
Во второй части необходимо будет настроить основные параметры коммутатора и компьютера.
            Шаг 1. Настройте базовые параметры коммутатора.
..*В режиме глобальной конфигурации скопируйте следующие базовые параметры конфигурации и вставьте их в файл на коммутаторе S1. 
no ip domain-lookup 
hostname S1 //задаём имя устройству
service password-encryption //включаем шифрование пароля
enable secret class //задаём пароль привелигированного ррежима
banner motd # 
Unauthorized access is strictly prohibited. # //задаём приветственное сообщение
..* Назначьте IP-адрес интерфейсу SVI на коммутаторе. Благодаря этому вы получите возможность удаленного управления коммутатором.

   ![](https://otuslabs.ucoz.net/no_sh_vlan1.jpg)

..* Доступ через порт консоли также следует ограничить  с помощью пароля. Используйте cisco в качестве пароля для входа в консоль в этом задании. Конфигурация по умолчанию разрешает все консольные подключения без пароля. Чтобы консольные сообщения не прерывали выполнение команд, используйте параметр logging synchronous.
S1(config)# line con 0
S1(config-line)# logging synchronous 

..* Настройте каналы виртуального соединения для удаленного управления (vty), чтобы коммутатор разрешил доступ через Telnet. Если не настроить пароль VTY, будет невозможно подключиться к коммутатору по протоколу Telnet.

![](https://otuslabs.ucoz.net/all_passwords.jpg)

Вопрос:
Для чего нужна команда login? **Команда login является одной из основных команд в программе Packet Tracer. Она используется для входа в устройство или систему, обычно с помощью учетных данных**

            Шаг 2. Настройте IP-адрес на компьютере PC-A.
Назначьте компьютеру IP-адрес и маску подсети в соответствии с таблицей адресации.
   PC-A config.jpg



##    Часть 3. Проверка сетевых подключений
В третьей части лабораторной работы вам предстоит проверить и задокументировать конфигурацию коммутатора, протестировать сквозное соединение между компьютером PC-A и коммутатором S1, а также протестировать возможность удаленного управления коммутатором.
            Шаг 1. Отобразите конфигурацию коммутатора.
            
![](https://otuslabs.ucoz.net/sw_conf1.jpg)
![](https://otuslabs.ucoz.net/sw_conf2.jpg)

S1#sh run
Building configuration...

Current configuration : 1336 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
!
hostname S1
!
enable secret 5 $1$mERr$9cTjUIEqNGurQiFU.ZeCi1
!
!
!
no ip domain-lookup
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
!
interface FastEthernet0/2
!
interface FastEthernet0/3
!
interface FastEthernet0/4
!
interface FastEthernet0/5
!
interface FastEthernet0/6
 switchport mode access
!
interface FastEthernet0/7
!
interface FastEthernet0/8
!
interface FastEthernet0/9
!
interface FastEthernet0/10
!
interface FastEthernet0/11
!
interface FastEthernet0/12
!
interface FastEthernet0/13
!
interface FastEthernet0/14
!
interface FastEthernet0/15
!
interface FastEthernet0/16
!
interface FastEthernet0/17
!
interface FastEthernet0/18
!
interface FastEthernet0/19
!
interface FastEthernet0/20
!
interface FastEthernet0/21
!
interface FastEthernet0/22
!
interface FastEthernet0/23
!
interface FastEthernet0/24
!
interface GigabitEthernet0/1
!
interface GigabitEthernet0/2
!
interface Vlan1
 ip address 192.168.1.2 255.255.255.0
!
banner motd ^C Unauthorized acces is stricly prohibited! ^C
!
!
!
line con 0
 password 7 0822455D0A16
 logging synchronous
 login
!
line vty 0 4
 password 7 0822455D0A16
 logging synchronous
 login
line vty 5 15
 login
!
!
!
!
end

..* Проверьте параметры VLAN 1.
S1# show interface vlan 1 

![](https://otuslabs.ucoz.net/sh_vlan1_2.jpg)

Какова полоса пропускания этого интерфейса? **BW 100000 Kbit**
В каком состоянии находится VLAN 1? **Vlan1 is up** 
В каком состоянии находится канальный протокол? **line protocol is up**

Шаг 2. Протестируйте сквозное соединение, отправив эхо-запрос.

![](https://otuslabs.ucoz.net/Ping_PC.jpg)

..*Проверьте удаленное управление коммутатором S1.

![](https://otuslabs.ucoz.net/SSH.jpg)
