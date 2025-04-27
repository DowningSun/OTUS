en
conf t
no ip domain lookup

host R2HQ    //Задаём имя хоста

int E0/0         //настройка внешнеего интерфейса и включение NAT
 no sh
 ip address 90.140.60.140 255.255.255.240
 ip nat outside
exit

int E0/2  // Настройка внутреннего интерфейса +NAT
 no sh 
 ip address 10.10.10.2 255.255.255.0
 ip nat inside
 standby 1 ip 10.10.10.248 // Адрес для HSRP
 standby 1 priority 110 //Приоритет выше
 standby 1 preempt //Позволяет роутеру с более высоким приоритетом взять на себя роль активного маршрутизатора
exit

ip sla 2
 icmp-echo 77.88.8.8 source-ip 10.10.10.2 //проверяем доступность узла 77.88.8.8
 frequency 10 //каждые 10 секунд
ip sla schedule 2 life forever start-time now //вечно

track 2 ip sla 2 reachability //отслеживаем состояние IP SLA

interface GigabitEthernet0/0
 standby 1 track 2 decrement 20   //Уменьшает приоритет HSRP на заданное значение (в данном случае 20), если отслеживаемый объект недоступен.
 
 
 
 
