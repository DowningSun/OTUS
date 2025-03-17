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


