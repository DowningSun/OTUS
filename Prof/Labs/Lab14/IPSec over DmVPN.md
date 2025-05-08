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

 
После включения IPSec на R15, но до момента включения на R18 получаем сообщение, что устройство приняло GRE-пакет (protocol 47), не инкапсулированный в IPsec, тогда как ожидался IPsec-защищённый GRE.

![image](https://github.com/user-attachments/assets/b14cc759-bb34-4f6e-83bb-5c606b357454)

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

Теперь проверяем, что появился  IPSec туннель. Статус  QM_IDLE говорит, что все работает штатно. Это подтверждает, что IKE SA установлены и в состоянии QM_IDLE, что нормально для IKEv1 Main Mode (Phase 1 завершена) + Quick Mode (Phase 2 активна). То есть IPsec SA работают

![image](https://github.com/user-attachments/assets/3619c4b1-9112-494a-8f7c-f68881203e1c)

Пинг-чек + трассировка показывают, что узлы доступны, трафик идёт через туннель

![image](https://github.com/user-attachments/assets/9fc13282-749a-4c81-8b5a-46d326235df4)

Создадим резерв на R14

        crypto isakmp policy 1
        encr aes
        authent pre-share       
        group 2
        crypt isa key 123123123 addr 200.200.30.18

        crypto ipsec transform-set GRE-TSET esp-aes esp-sha-hmac
        mode transport

        crypto ipsec prof GRE-PROF
        set transform-set GRE-TSET

        int tun 0
        tun prot ipsec prof GRE-PROF

Ппока R18 не имеет настройки для связи с R14 получаем сообщение

![image](https://github.com/user-attachments/assets/05c50c84-9270-4378-84bd-4724aa48c149)

На R18 добавить команду для создания мини-туннеля 1 фазы

crypto isakmp key 123123123 address 100.100.20.14

На роутере R18 настроить интерфейс Tunnel 1 (это второй туннель на этом роутере для GRE между R18 и R14). Настройки GRE over IPSec сводятся лишь к тому, что необходимо добавить в интерфей Tunnel 1 профиль GRE-PROF

interface Tunnel1
tunnel protection ipsec profile GRE-PROF


## 2. Настроите DMVPN поверх IPSec между Москва и Чокурдах, Лабытнанги

На роутерах R27 Лабытнаги и R28 Чокурдах настроить первую и вторую фазы IPSec, настройка профиля IPSec.

        crypto isakmp policy 1
        encr aes
        authentication pre-share
        group 2
        crypto isakmp key 123123123 address 100.100.20.15
        crypto isakmp key 123123123 address 100.100.20.14
        crypto ipsec transform-set GRE-PROF esp-aes esp-sha-hmac
        mode transport
        crypto ipsec profile GRE-PROF
        set transform-set GRE-PROF

На R27 и R28 на туннельном интерфейсе Tunnel0 привязать профиль IPSec

        interface Tunnel0
        tunnel protection ipsec profile GRE-PROF

На роутерах R14 и R15 ввести команды для организации мини-туннеля 1 фазы

 На R14

        crypto isakmp key 123123123 address 120.120.120.2 - для Лабытнаги
        crypto isakmp key 123123123 address 130.130.130.2 - для Чокурдах

На R15

        crypto isakmp key 123123123 address 120.120.120.2 - для Лабытнаги
        crypto isakmp key 123123123 address 130.130.130.2 - для Чокурдах 

3. * Для IPSec использовать CA и сертификаты
  
R15

        ip domain-name otus.ru
        ip http server
        crypto key generate rsa general-keys label CA exportable modulus 2048      

        crypto pki server CA
        no shut

R27

        crypto key generate rsa label VPN
        The name for the keys will be: VPN
        Choose the size of the key modulus in the range of 360 to 4096 for your
        General Purpose Keys. Choosing a key modulus greater than 512 may take
        a few minutes.

        How many bits in the modulus [512]: 2048
        % Generating 2048 bit RSA keys, keys will be non-exportable...
        [OK] (elapsed time was 2 seconds)

        R27(config)#crypto pki trustpoint VPN
        R27(ca-trustpoint)#enrollment url http://172.16.10.1
        R27(ca-trustpoint)#subject-name CN=R27,OU=VPN,O=OTUS,C=RU 
        R27(ca-trustpoint)#rsakeypair VPN
        R27(ca-trustpoint)#revocation-check none

        R27(config)#crypto pki authenticate VPN
        Certificate has the following attributes:
        Fingerprint MD5: B844D35E A124ABF4 D3ABF093 496B3646
        Fingerprint SHA1: 39AB1E92 EE241F8D 129BCD54 468562A0 7EBB7AC1
 

        % Do you accept this certificate? [yes/no]: yes
        Trustpoint CA certificate accepted.
        R27(config)#crypto pki enroll VPN

        Start certificate enrollment .. 
        Create a challenge password. You will need to verbally provide this
        password to the CA Administrator in order to revoke your certificate.
        For security reasons your password will not be saved in the configuration.
        Please make a note of it.

        Password: 
        Re-enter password: 

        % The subject name in the certificate will include: CN=R27,OU=VPN,O=OTUS,C=RU
        % The subject name in the certificate will include: R27
        % Include the router serial number in the subject name? [yes/no]: no
        % Include an IP address in the subject name? [no]: no
        Request certificate from CA? [yes/no]: yes
        % Certificate request sent to Certificate Authority
        % The 'show crypto pki certificate verbose VPN' commandwill show the fingerprint.

        *May  8 07:41:08.081: CRYPTO_PKI:  Certificate Request Fingerprint MD5: 540CF5E2 C4C2BC43 8066D2A8 613FD25C
        *May  8 07:41:08.081: CRYPTO_PKI:  Certificate Request Fingerprint SHA1: 0A488727 23E6C4C9 803A152D 17045ECE 96024B3B

Запрос сертификата от R27

![image](https://github.com/user-attachments/assets/a30ca5ec-f1d4-40ea-85f3-a0ef366f9048)

![image](https://github.com/user-attachments/assets/4cd99a2c-7b0e-43de-81b9-f92b9a54bd1c)

Аналогичный набор команд делаем на других роутерах, которые будут проходить авторизацию через сертифика.

Далее необходимо изменить политику
        crypto isakmp policy 10
        authentication rsa-sig 


        
        
