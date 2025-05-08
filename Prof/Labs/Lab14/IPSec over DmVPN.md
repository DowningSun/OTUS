# Цель:

В этой самостоятельной работе мы ожидаем, что вы самостоятельно:

    Настроите GRE поверх IPSec между офисами Москва и С.-Петербург.
    
    Настроите DMVPN поверх IPSec между Москва и Чокурдах, Лабытнанги.
    
    Все узлы в офисах в лабораторной работе должны иметь IP связность.
    
    План работы и изменения зафиксированы в документации.


## Настройка IPSec

На роутерее R15

1 Фаза

    crypto isakmp policy 1
    encr aes
    authent pre-share       
    group 2
    crypt isa key 123123123 addr 200.200.30.18

2 Фаза

    crypto ipsec transform-set GRE-TSET esp-aes esp-sha-hmac
    mode transport
    
настройка профиля IPSec

    crypto ipsec prof GRE-PROF
    set transform-set GRE-TSET

Для привязки настроек к Tunnel 0 необходимо вместо crypto map использовать crypto ipsec profile

    int tun 0
    tun prot ipsec prof GRE-PROF

  На роутерее R18 создаются аналогичные настрройки

    crypto isakmp policy 1         создает политики шифрования для 1 фазы
    encr aes    устанавливает шифрование
    authentication pre-share    аутентификация по ключу
    group 2  передеача хеша ключа по DH
    crypto isakmp key 123123123 address 100.100.20.15    ключ для связи с IP роутера R15 Москва

    crypto ipsec transform-set GRE-TSET esp-aes esp-sha-hmac     режим для шифрования пользовательских данных с использованием протокола ESP
    mode transport    режим туннеля. В режиме transport не создается новый IP заголовок как в tunnel режиме.

    crypto ipsec profile GRE-PROF    создать профиль IPSec с именем GRE-PROF
    set transform-set GRE-TSET    привязать настройки 2 фазы к профилю.

    int tun 0    применяяем настройки к интерфейсу Tun0
    tunnel protection ipsec profile GRE-PROF













    
R18(config-if)#tunnel protection ipsec profile GRE-PROF
