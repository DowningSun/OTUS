#### PBR
### Цель:
Настроить политику маршрутизации в офисе Чокурдах

Распределить трафик между 2 линками

Описание/Пошаговая инструкция выполнения домашнего задания:
В этой самостоятельной работе мы ожидаем, что вы самостоятельно:

1. Настроите политику маршрутизации для сетей офиса.
2. Распределите трафик между двумя линками с провайдером.
3. Настроите отслеживание линка через технологию IP SLA.(только для IPv4)
4. Настройте для офиса Лабытнанги маршрут по-умолчанию.
5. План работы и изменения зафиксированы в документации

### 1.Настроите политику маршрутизации для сетей офиса

![image](https://github.com/user-attachments/assets/bc8fd13c-f38e-448e-90dc-335a80022c5e)

Для решения задачи была выбрана динамическая маршрутизация (OSPF) между роутерами R23, R25, R26, R28. Настройка протокола OSPF производилась на интерфейсах маршрутизаторов.

Пример конфига для всех интерфейсов учавствующих в OSPF.

interface EthernetX/Z

ip address a.a.a.a m.m.m.m

ip ospf network point-to-point

ip ospf 1 area 0

Данные команды применены для всех интерфейсов где включен OSPF

* Роутер R23 - интерфейс e0/1
* Роутер R25 - интерфейсы e0/0, e0/2, e0/3
* Роутер R26 - интерфейсы e0/2, e0/1
* Роутер R28 - интерфейсы e0/0, e0/1, e0/2.60, e0/2.70

| Сеть     |  Порт       |
|-----------------:|:---------------|
| 10.10.10.0/30    | R23e0/1 R25e0/0|   
| 10.10.10.4/30    | R25e0/2 R26e0/2|             
| 130.130.130.0/28 | R25e0/3 R28e0/1|
| 140.140.140.0/28 | R26e0/1 R28e0/0|
| 192.168.60.0/24  |   R28e0/2.60   |
| 192.168.70.0/24  |   R28e0/2.70   |

### 2. Распределите трафик между двумя линками с провайдером.

После запуска OSPF трафик к узлу 10.10.10.1 с устройств VPC30 и VPC31 идёт через узел R25.

![image](https://github.com/user-attachments/assets/cffedb9f-7304-45cf-9045-3b167ab45366)

![image](https://github.com/user-attachments/assets/43c38c0e-38ad-4f11-8389-16a8d461ab2d)


Для распределения трафика создадим ACL для сети 192.168.70.0:

      R28(config)#ip access-list extended PC
      R28(config-ext-nacl)#permit ip 192.168.70.0 0.0.0.255 any

Создадим route-map с соответствием проверки сети 192.168.70.0

      R28(config)#route-map PC
      R28(config-route-map)#match ip address PC
      R28(config-route-map)#set ip next-hop 140.140.140.1

Присвоем созданный route-map интерфейсу e0/2.70

      R28(config)#int e0/2.70
      R28(config-subif)#ip policy route-map PC

после применения route-map прохождение трафика стало выгляядить следующим образом:

![image](https://github.com/user-attachments/assets/6dbcceb7-f9d0-4536-89f4-7ec6ac3d6f7f)

![image](https://github.com/user-attachments/assets/71590277-1692-402c-8725-e989a8a6aa91)

Задача распределения трафика между ISP решена.

#### 3. Настроите отслеживание линка через технологию IP SLA

Порядок действий:

1. Создать IP SLA
2. Задать параметр по которому SLA будет пинговать шлюз маршрута по умолчанию с адреса источника
3. Задать частоту пинга
4. Задать постоянный пинг и запустить задачу прямо сейчас

       R28(config)#ip sla 1
       R28(config-ip-sla)#icmp-echo 130.130.130.1 source-ip 130.130.130.2
       R28(config-ip-sla)#frequency 5
       R28(config)#ip sla schedule 1 life forever start-time now
       
  Далее необходимо настроить track  отслеживания, который будет проверять действие ip sla. 
   
     R28(config)#track 1 ip sla 1 reachability
  
 Создать маршрут по умолчанию с треком отслеживания 
 
    R28(config)#ip route 0.0.0.0 0.0.0.0 130.130.130.1 track 1
    
  Как выглядят настройки маршрутизатора с доступным маршрутом по умолчанию:
В конфиге находятся два маршрута:
   
    ip route 0.0.0.0 0.0.0.0 130.130.130.1 track 1
    ip route 0.0.0.0 0.0.0.0 140.140.140.1 20


sh ip route возвращает следующие маршруты:

![image](https://github.com/user-attachments/assets/dc900f25-ff32-417b-918b-eb65e9b2775f)

в случае падения узла 130.130.130.1 таблица маршрутизации меняется 

![image](https://github.com/user-attachments/assets/0ca0ce16-da1f-46e3-8deb-c10e6b9624cd)


#### 4.Настройте для офиса Лабытнанги маршрут по-умолчанию

Самая сложная часть задания решается следующим набором действий:

Сеть между данными роутерами:

| Сеть     |  Узел       |
|-----------------:|:---------------|
| 120.120.120.0/28 | R25e0/1 R27e0/0|

Для настройки маршрута по умолчанию на роутере R27 необходимо выполнить следующую команду:

      R27(config)#ip route 0.0.0.0 0.0.0.0 120.120.120.1 
