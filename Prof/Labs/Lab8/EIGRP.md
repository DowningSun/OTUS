## EIGRP

Задание:

Цель: Настроить EIGRP в С.-Петербург; Использовать named EIGRP

В офисе С.-Петербург настроить EIGRP
R32 получает только маршрут по-умолчанию
R16-17 анонсируют только суммарные префиксы
Использовать EIGRP named-mode для настройки сети

## Схема сети

![image](https://github.com/user-attachments/assets/70539f1e-6b33-472d-9e7c-ccfc6c45188b)

## Конфигурация маршрутизаторов осуществляется следующим способом:

1. Включаем на всех роутерах процес EIGRP в режиме Named Mode. 
          
       RX(config)#router eigrp SPB - команда включает процес EIGRP в named mode с именем SPB. 
 
2. Производим настройку динамической маршрутизации для IPv4 и добавляем Router ID к каждой address-family

       RX(config-router)#address-family ipv4 unicast autonomous-system 100
       RX(config-router)#address-family ipv6 unicast autonomous-system 100
       RX(config-router-af)#eigrp router-id x.x.x.x
       
       
3. Интерфейсы роутеров добавляем к анонсу EIGRP IPv4
 
       RX(config-router-af)#network 0.0.0.0
       
4.  Следующая команда позволяет задать общие настройки для всех интерфейсов учавствующих в обмене EIGRP пакетами.

        RX(config-router-af)#af-interface default
        
5.  Данная команда отключает процес обмена EIGRP пакетами на всех интерфейсах.

        RX(config-router-af-interface)#shutdown
    
6.  Настройка обмена EIGRP пакетами на конкретном интерфейсе

        RX(config-router-af)#af-interface ethernet - вход в настройку интерфейса учавствующего в обмене EIGRP пакетами.
        RX(config-router-af)#no shutdown - включаем обмен EIGRP пакетами конкретно на необходимом интерфейсе



![image](https://github.com/user-attachments/assets/82097ccb-04dc-4372-aac5-dd94e156ee87)

## Псоле проведенных настройках таблица маршрутизации выглядит следжующим образом:

![image](https://github.com/user-attachments/assets/62a3e3f3-9f93-4c96-bb6d-e4dc5bdd9fbb)

Сети LoopBack X.X.X.X - где X это цифра номера маршрутизатора согласно схемы. Пример 17.17.17.17 - роутер R17, 32.32.32.32 - роутер R32

## 2. R32 получает только маршрут по-умолчанию.

Для выполнения данного задания необходимо на маршрутизаторе R16 ввести следующие команды.

     R16(config)#router eigrp PITER
     R16(config-router)#address-family ipv4 unicast autonomous-system 100
     R16(config-router-af)#af-interface e0/3 - этот инерфейс смотрит на маршрутизатор R32
     R16(config-router-af-interface)#summary-address 0.0.0.0 0.0.0.0

После ввода данных команд таблица маршрутизации роутера R32 соответствует заданию.

![image](https://github.com/user-attachments/assets/caa7a31f-8856-403d-bc97-0ee7c68a186d)

## 3 .R16-17 анонсируют только суммарные префиксы. 

Для выполнения данного задания необходимо проссумировать маршруты на R17 и R16 и отдать их в сторону R18 для сокращения таблицы маршрутизации.

До суммирования, таблица маршрутизации R18 выглядит следующим образом:

 R16-17(config)#router eigrp PITER
     R16-17(config-router)#address-family ipv4 unicast autonomous-system 100
     R16-17(config-router-af)#af-interface e0/1
     R16-17(config-router-af-interface)#summary-address 30.30.30.0 255.255.255.240

Сети 10.10.30.0/30, 10.10.30.4/30, 10.10.30.8/30, 10.10.30.12/30 проссумировались в общую сеть 10.10.30.0/28
