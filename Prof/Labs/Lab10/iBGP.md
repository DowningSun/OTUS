### iBGP

## Цель:

Настроить iBGP в офисе Москва

Настроить iBGP в сети провайдера Триада

Организовать полную IP связанность всех сетей

Настроите iBGP в офисом Москва между маршрутизаторами R14 и R15.

Настроите iBGP в провайдере Триада, с использованием RR.

Настройте офиса Москва так, чтобы приоритетным провайдером стал Ламас.

Настройте офиса С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно.

Все сети в лабораторной работе должны иметь IP связность.

## 1. Настроите iBGP в провайдере Триада

### Схема сети

![image](https://github.com/user-attachments/assets/337aae22-d220-4586-994f-59aaad911139)

### Конфигурация маршрутизаторов для iBGP соседства аналогична настройкам eBGP и сводиться к следующим настройкам:

1. Включаем на всех роутерах процес BGP с номером AS согласно схемы. 
          
       RX(config)#router BGP X - где X номер автономной системы.
           
 Роутеры учавствующие в обмене:
 
    R23 - Триада
    R24 - Триада
    R25 - Триада 
    R26 - Триада 
   
 2. Назначаем на каждом роутере Router ID для BGP. 

       RX(config)#router bgp X
       RX(config-router)#bgp router-id x.x.x.x - где x.x.x.x номер роутера согласно схемы. R23 - router-id 23.23.23.23
     
     
3. Устанавливаем сосдество по iBGP согласно задания.
   
Для установления соседства по iBGP нужно устанавливать соседство каждый с каждым (Full Mesh) Предподчтителнее использовать LoopBack интерфейсы

       
 Пример для роутреа R23
 
       R23 - Триада (23.23.23.23)  - R24 - Триада (24.24.24.24)
       R23 - Триада (23.23.23.23)  - R25 - Триада (25.25.25.25)
       R23 - Триада (23.23.23.23)  - R26 - Триада (26.26.26.26)
       
       
  Для установления BGP соседства необходимо на каждом роутере указать команду:
  
       RX(config)#router bgp X
       RX(config-router)#neighbor x.x.x.x remote-as Z - где x.x.x.x IP соседа с кем устанавливается BGP, а Z - номер автономной системы BGP соседа
       RX(config-router)#neighbor x.x.x.x update-source loopback0 - команда update-source указывает из под какого интрефейса устанавливать соседство. В данном случае интерфейс loopback0

### Пример R23 Триада
  
       router bgp 520
       bgp log-neighbor-changes
       neighbor 24.24.24.24 remote-as 520
       neighbor 24.24.24.24 update-source Loopback0
       neighbor 25.25.25.25 remote-as 520
       neighbor 25.25.25.25 update-source Loopback0
       neighbor 26.26.26.26 remote-as 520
       neighbor 26.26.26.26 update-source Loopback0
       
### Пример R24 Триада

      router bgp 520
      bgp router-id 24.24.24.24
      bgp log-neighbor-changes
      neighbor 23.23.23.23 remote-as 520
      neighbor 23.23.23.23 update-source Loopback0
      neighbor 25.25.25.25 remote-as 520
      neighbor 25.25.25.25 update-source Loopback0
      neighbor 26.26.26.26 remote-as 520
      neighbor 26.26.26.26 update-source Loopback0
      neighbor 26.26.26.26 next-hop-self
      neighbor 90.90.90.1 remote-as 301
      neighbor 100.100.100.2 remote-as 2042

После выполнения данных команд на каждом роутере создается BGP соседвство.

![image](https://github.com/user-attachments/assets/ac0ba2e9-bd37-4c70-9c0b-51e7f930cfd4)

![image](https://github.com/user-attachments/assets/8be00ba1-d766-4bcf-a2d4-3371944c94b5)

## 2. iBGP в офисе Москва между маршрутизаторами R14 и R15

iBGP между роутерами R14 и R15 аналогична настройкам iBGP Триада

1. Включаем на всех роутерах процес BGP с номером AS согласно схемы. 
          
       RX(config)#router BGP X - где X номер автономной системы.
      
Роутеры учавствующие в обмене:
 
    R14 - Москва
    R15 - Москва
    
2. Назначаем на каждом роутере Router ID для BGP.

       RX(config)#router bgp X
       RX(config-router)#bgp router-id x.x.x.x - где x.x.x.x номер роутера согласно схемы. R14 - router-id 14.14.14.14

3. Устанавливаем сосдество по iBGP согласно задания.

       R14 - Москва (14.14.14.14)  - R15 - Москва (15.15.15.15)
       R15 - Москва (15.15.15.15)  - R14 - Москва (14.14.14.14)

![image](https://github.com/user-attachments/assets/5ab74151-15c6-4dbf-ace3-7fb85154ce74)

![image](https://github.com/user-attachments/assets/158c5451-475c-4928-b15b-8269e8077b69)

## 3. Настройте офис Москвы так, чтобы приоритетным провайдером стал Ламас.

Схема сети Москва-Ламас

![image](https://github.com/user-attachments/assets/88eb7e8f-542e-4b92-bbf9-5da564574f22)

Trace VPC показывает, что маршрут идет через Киторн (50.50.50.1)

![image](https://github.com/user-attachments/assets/bf3801ff-b74e-4906-9ef9-152b5c1da6eb)

Решить задачу можно следующим образом:

      R15(config)# route-map LAMAS permit 10 - создаем route-map
      
      R15(config-route-map)# set local-preference 150 - устанавливаем local-preference в значение 150 для всех маршрутов, которые мы получим от соседа
      
      R15(config-router)#neighbor 60.60.60.1 route-map LAMAS in - применяем route-map к соседу

![image](https://github.com/user-attachments/assets/18a3f29e-8c15-4f79-a778-d92e32cd883a)

Из скриншота видно, что на роутере R14 изменился LocPrf для префиксов 192.168.40.0 и 192.168.50.0, но в таблице BGP по прежнему в приоритете остается маршрут через Киторн (50.50.50.1).

Это связанно с тем, что Update пришедший от роутера R21 Ламас на роутер R15 Москва содержит Next-Hop 60.60.60.1 и далее R15 передает этот Update по iBGP на роутер R14 не меняя Next-Hop.

Соответственно у роутреа R14 нет маршрута до сети 60.60.60.0.

Для решения этой проблемы необходимо на роутере R15 нужно указать next-hop-self:

     R15(config-router)#neighbor 14.14.14.14 next-hop-self

После применения команды маршрутизация осуществляется через R15:

![image](https://github.com/user-attachments/assets/0e7553a2-7bb4-4077-8110-2802c86ea410)

## 4. Настройте офис С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно

На роутере R18 примем:

    R18(config-router)#maximum-paths 2

Префиксы получаемые от Москвы имеют Next Hop 90.90.90.1. Но R26 не знает как добраться до сети 90.90.90.0 /24. Так как Update от R24 идущий к R26 не меняет Next Hop. Исправим это и на R24 введем следующую команду

     R24(config-router)#neighbor 26.26.26.26 next-hop-self

Теперь R26 имеет Next Hop 24.24.24.24 до сетей Москвы и дальше передает данный Update на R18

![image](https://github.com/user-attachments/assets/038d01df-54dd-4b6f-b258-f087a25ff956)

![image](https://github.com/user-attachments/assets/2b5e524e-0d06-4f11-b0ac-1538ac333baa)

Теперь сети Москвы доступны по двум путям.

## 5. Все сети в лабораторной работе должны иметь IP связность

Т.к. конечные пользовательские сети были анонсированы в предыдущих заданиях осталось только анонсировать MGMNT сети.

Для анонса отдадим суммированный маршрут соответствующих сетей.

На роутерах R14 и R15 нужно выполнить команду:

     R14-15(config-router)#network 20.20.20.0 mask 255.255.255.0

Поскольку BGP проверяет точное совпадение в таблице маршрутизации для того, что бы анонсировать сеть соседям, но такой сети в таблице нет,её необходимо добавить командой:

          R14-15(config)ip route 20.20.20.0 255.255.255.0 Null0 

Такой же набор команд надо выполнить для С.-Петербурга

          R18(config-router)#network 30.30.30.0 mask 255.255.255.0
          R18(config)ip route 10.10.30.0 255.255.255.0 Null0 

После выполнения данных команд на роутерах Москва и Питер в таблице маршрутизации появились следующие сети:

![image](https://github.com/user-attachments/assets/bf9157eb-b676-4989-8ab2-6e528ef4c087)

![image](https://github.com/user-attachments/assets/478dd6bc-6bbf-4fb8-b426-45279988b8f7)

![image](https://github.com/user-attachments/assets/5de81e5e-7141-4b79-89b6-fbe82b0909eb)


Проверим связность PING-ом оборудования

![image](https://github.com/user-attachments/assets/4ff6edb3-a588-4f33-9ca7-725a637a1206)






Заметка для себя:
П.3 не работал долго, были испробованы разные методы, заработал после того, как заглушил виртуалку EVE-NG и запустил снова.

"Дергающийся глаз" SW5 (просто сам выключался) исчез после того, как "разобрал" etherchanel и собрал обратно без внесения изменений
