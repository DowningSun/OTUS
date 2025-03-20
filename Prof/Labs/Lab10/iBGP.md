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
