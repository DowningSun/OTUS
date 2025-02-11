## Лабораторная работа - Реализация DHCPv4 

**Топология**

![Topology](https://github.com/DowningSun/OTUS/assets/156109695/bfe8a2e4-ce9e-48cc-81f6-2e09bc319ca9)

#### Таблица адресации

| Устройство | Интерфейс | IP-адрес | Маска подсети | Шлюз по умолчанию |
| ------ | ------ | ------ |  ------ | ------ |
| R1 | G0/0/0 | 10.0.0.1 | 255.255.255.252 |  |
|  | G0/0/1 |   |   |  |
|  | G0/0/1.100  | 192.168.1.65  | 255.255.255.192  |  |
|  | G0/0/1.200 | 192.168.1.129  | 255.255.255.224  |  |
|  | G0/0/1.1000 |   |   |  |
| R2 | G0/0 | 10.0.0.2 | 255.255.255.252 |  |
|  | G0/0/1 | 192.168.1.161  | 255.255.255.240 |  |
| S1 | VLAN 200 | 192.168.1.130  | 255.255.255.224  | 192.168.1.129 |
| S2 | VLAN 1 |   |   |  |
| PC-A | NIC  | DHCP  | DHCP  | DHCP |
| PC-B | NIC  | DHCP  | DHCP  | DHCP |

#### Таблица VLAN

| VLAN | Имя | Назначенный интерфейс |
| ------- | ------ | ------ |
| 1 | Нет  | S2: F0/18 |
| 100 | Клиенты  | S1: F0/6  |
| 200 | Управление  | S1: VLAN 200   |
| 999 | Parking_Lot  | S1: F0/1-4, F0/7-24, G0/1-2 |
| 1000 | Собственная  |  |

**Выполнение работы**

#### Часть 1. Создание сети и настройка основных параметров устройства

**Шаг 1. Создание схемы адресации**

Подсеть сети 192.168.1.0/24 в соответствии со следующими требованиями:

a. Одна подсеть «Подсеть A», поддерживающая 58 хостов (клиентская VLAN на R1).
Подсеть A **192.168.1.64/26**
Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.100 . **192.168.1.65**

b. Одна подсеть «Подсеть B», поддерживающая 28 хостов (управляющая VLAN на R1). 
Подсеть B: **192.168.1.128/27**
Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.200. **192.168.1.129**
Запишите второй IP-адрес в таблице адресов для S1 VLAN 200 и введите соответствующий шлюз по умолчанию. **192.168.1.129**

c. Одна подсеть «Подсеть C», поддерживающая 12 узлов (клиентская сеть на R2).
Подсеть C: **192.168.1.160/28**
Запишите первый IP-адрес в таблице адресации для R2 G0/0/1. **192.168.1.161**

**Шаг 2. Произведите базовую настройку маршрутизаторов.**
a. Назначьте маршрутизатору имя устройства.
Откройте окно конфигурации
b. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c. Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d. Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e. Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

f. Зашифруйте открытые пароли.

g. Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

i. Установите часы на маршрутизаторе на сегодняшнее время и дату.

![V4_Rou_DefConf](https://github.com/DowningSun/OTUS/assets/156109695/19ae1caf-e663-4380-910c-5a1da175dac4)

**Шаг 3.Настройка маршрутизации между сетями VLAN на маршрутизаторе R1**

a. Активируйте интерфейс G0/0/1 на маршрутизаторе.

b. Настройте подинтерфейсы для каждой VLAN в соответствии с требованиями таблицы IP-адресации. Все субинтерфейсы используют инкапсуляцию 802.1Q и назначаются первый полезный адрес из вычисленного пула IP-адресов. Убедитесь, что подинтерфейсу для native VLAN не назначен IP-адрес. Включите описание для каждого подинтерфейса.

c. Убедитесь, что вспомогательные интерфейсы работают.

![V4_R1subs](https://github.com/DowningSun/OTUS/assets/156109695/6bdb05cd-24aa-4098-a04c-c4ac89f2009e)

**Шаг 4. Настройте G0/1 на R2, затем G0/0/0 и статическую маршрутизацию для обоих маршрутизаторов**

a. Настройте G0/0/1 на R2 с первым IP-адресом подсети C, рассчитанным ранее.

b. Настройте интерфейс G0/0/0 для каждого маршрутизатора на основе приведенной выше таблицы IP-адресации.

c. Настройте маршрут по умолчанию на каждом маршрутизаторе, указываемом на IP-адрес G0/0/0 на другом маршрутизаторе.

d. Убедитесь, что статическая маршрутизация работает с помощью пинга до адреса G0/0/1 R2 от R1.

e. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

![V4_R1R2_route_ping](https://github.com/DowningSun/OTUS/assets/156109695/e7594053-d3d8-4ac7-9853-cf51654747e8)

**Шаг 5. Настройте базовые параметры каждого коммутатора.**

a. Присвойте коммутатору имя устройства.

b. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c. Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d. Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e. Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

f. Зашифруйте открытые пароли.

g. Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

i. Установите часы на маршрутизаторе на сегодняшнее время и дату.

j. Скопируйте текущую конфигурацию в файл загрузочной конфигурации.

![V4_SwDefaultConf](https://github.com/DowningSun/OTUS/assets/156109695/a461ba6d-fba8-4088-9626-206b00918e00)

**Шаг 6. Создайте сети VLAN на коммутаторе S1.**

Примечание. S2 настроен только с базовыми настройками. 

a. Создайте необходимые VLAN на коммутаторе 1 и присвойте им имена из приведенной выше таблицы.

b. Настройте и активируйте интерфейс управления на S1 (VLAN 200), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того установите шлюз по умолчанию на S1. 

**S1(config-if-range)#int vlan 200
S1(config-if)#
%LINK-5-CHANGED: Interface Vlan200, changed state to up
S1(config-if)#ip add 192.168.1.130 255.255.255.224
S1(config-if)#ip default-gateway 192.168.1.129**

![V4_S1_VLANs](https://github.com/DowningSun/OTUS/assets/156109695/e27c5cc2-3c10-461b-a471-a72b1f4e492f)

c. Настройте и активируйте интерфейс управления на S2 (VLAN 1), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того, установите шлюз по умолчанию на S2

d. Назначьте все неиспользуемые порты S1 VLAN Parking_Lot, настройте их для статического режима доступа и административно деактивируйте их. На S2 административно деактивируйте все неиспользуемые порты.

![V4_S2_Int_sh](https://github.com/DowningSun/OTUS/assets/156109695/e3af021f-3ba8-4841-b07c-9149ce3ca8c1)

**Шаг 7. Назначьте сети VLAN соответствующим интерфейсам коммутатора.**

a. Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.

b. Убедитесь, что VLAN назначены на правильные интерфейсы.

![V4_S1_Vlan_Br](https://github.com/DowningSun/OTUS/assets/156109695/4a3e572a-f755-4a16-93b6-f5889447cc37)

Вопрос:
Почему интерфейс F0/5 указан в VLAN 1? **Потому, что данный VLAN существует, и действий с указанным портом, по присвоению в джругие VLAN не проводилось**

**Шаг 8. Вручную настройте интерфейс S1 F0/5 в качестве транка 802.1Q.**

a. Измените режим порта коммутатора, чтобы принудительно создать магистральный канал.

b. В рамках конфигурации транка  установите для native  VLAN значение 1000.

c. В качестве другой части конфигурации магистрали укажите, что VLAN 100, 200 и 1000 могут проходить по транку.

d. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

e. Проверьте состояние транка.

![V4_S1_Vlan_Trunk](https://github.com/DowningSun/OTUS/assets/156109695/17c2fd1f-6f01-4631-8974-12bf59011c30)

**поскольку ранее по заданию мы не создавали VLAN 1000 на устройстве,он был добавлен сейчас**

Вопрос:
Какой IP-адрес был бы у ПК, если бы он был подключен к сети с помощью DHCP? **Первый свободный из заданного пула адресов**

#### Часть 2. Настройка и проверка двух серверов DHCPv4 на R1

**Шаг 1. Настройте R1 с пулами DHCPv4 для двух поддерживаемых подсетей. Ниже приведен только пул DHCP для подсети A**

a. Исключите первые пять используемых адресов из каждого пула адресов. 

b. Создайте пул DHCP (используйте уникальное имя для каждого пула).

c. Укажите сеть, поддерживающую этот DHCP-сервер.

d. В качестве имени домена укажите CCNA-lab.com.

e. Настройте соответствующий шлюз по умолчанию для каждого пула DHCP.

f. Настройте время аренды на 2 дня 12 часов и 30 минут. **В CPT команда LEASE не поддерживается. Поэтому ввод LEASE 2 12 30  не даст результата**

g. Затем настройте второй пул DHCPv4, используя имя пула R2_Client_LAN и вычислите сеть, маршрутизатор по умолчанию, и используйте то же имя домена и время аренды, что и предыдущий пул DHCP.

![V4_R1_Make_Pool](https://github.com/DowningSun/OTUS/assets/156109695/3703c2df-9fdb-4b8d-8641-a6d4fb6c7f21)

**Шаг 3. Проверка конфигурации сервера DHCPv4**

a. Чтобы просмотреть сведения о пуле, выполните команду show ip dhcp pool .

b. Выполните команду show ip dhcp bindings для проверки установленных назначений адресов DHCP.

c. Выполните команду show ip dhcp server statistics для проверки сообщений DHCP. **Данная команда не обнаружена в списке поддерживаемых параметров**



**Шаг 4. Попытка получить IP-адрес от DHCP на PC-A**

a. Из командной строки компьютера PC-A выполните команду ipconfig /all.

b. После завершения процесса обновления выполните команду ipconfig для просмотра новой информации об IP-адресе.

c. Проверьте подключение с помощью пинга IP-адреса интерфейса R0 G0/0/1.

![V4_PCA_Test](https://github.com/DowningSun/OTUS/assets/156109695/e58d9feb-7a8c-4614-8c94-0f2bb69fec7c)

#### Часть 3. Настройка и проверка DHCP-ретрансляции на R2
В части 3 настраивается R2 для ретрансляции DHCP-запросов из локальной сети на интерфейсе G0/0/1 на DHCP-сервер (R1). 

**Шаг 1. Настройка R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/0/1**

a. Настройте команду ip helper-address на G0/0/1, указав IP-адрес G0/0/0 R1.

b. Сохраните конфигурацию.

![V4_R2_Helper](https://github.com/DowningSun/OTUS/assets/156109695/42943aa5-8768-4a1e-b6fd-01315379de12)

**Шаг 2. Попытка получить IP-адрес от DHCP на PC-B**

a. Из командной строки компьютера PC-B выполните команду ipconfig /all.

b. После завершения процесса обновления выполните команду ipconfig для просмотра новой информации об IP-адресе.

c. Проверьте подключение с помощью пинга IP-адреса интерфейса R1 G0/0/1.
                
d. Выполните show ip dhcp binding для R1 для проверки назначений адресов в DHCP.
                
e. Выполните команду show ip dhcp server statistics для проверки сообщений DHCP.

![V4_PCB_Test](https://github.com/DowningSun/OTUS/assets/156109695/5958bc5a-b374-4278-9676-ce522abcde68)

![image](https://github.com/DowningSun/OTUS/assets/156109695/58420afb-9d93-4b1f-9dae-437f365800f0)





## В силу того, что CPT имеет ограниченый набор команд, вторая часть будет выполнена в другой среде





## Лабраторная работа - Настройка DHCPv6 

**Топология**

![image](https://github.com/DowningSun/OTUS/assets/156109695/a7461ca3-f622-4747-98e8-1f7b6df19205)

**Таблица адресации**

| Устройство | Интерфейс | IPv6 адрес |
| ------ | ------ | ------ |
| R1 | E 0/0 | 2001:db8:acad:2::1/64|
| | | fe80::1 |
| | E /0/1 | 2001:db8:acad:1::1/64 |
| | | fe80::1 |
| R2 | E 0/0 | 2001:db8:acad:2::2/64 |
| | | fe80::2 |
| | E 0/1 | 2001:db8:acad:3::1/64 |
| | | fe80::1 |
| PC-A | NIC | DHCP |
| PC-B | NIC | DHCP |

**Ход работы**

#### Часть 1. Создание сети и настройка основных параметров устройства

**Шаг 1. Создайте сеть согласно топологии.**
**Шаг 2. Настройте базовые параметры каждого коммутатора. (необязательно)**

a. Присвойте коммутатору имя устройства.

b. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c. Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d. Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e. Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

f. Зашифруйте открытые пароли.

g. Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h. Отключите все неиспользуемые порты.

i. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

**Шаг 3. Произведите базовую настройку маршрутизаторов.**

a. Назначьте маршрутизатору имя устройства.

b. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c. Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d. Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e. Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

f. Зашифруйте открытые пароли.

g. Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h. Активация IPv6-маршрутизации

i. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

**Шаг 4. Настройка интерфейсов и маршрутизации для обоих маршрутизаторов**

a. Настройте интерфейсы E 0/0 и E 0/1 на R1 и R2 с адресами IPv6, указанными в таблице выше.

b. Настройте маршрут по умолчанию на каждом маршрутизаторе, который указывает на IP-адрес E 0/0 на другом маршрутизаторе.

c. Убедитесь, что маршрутизация работает с помощью пинга адреса E 0/1 R2 из R1

d. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

![image](https://github.com/user-attachments/assets/b9c179f1-eea7-4008-a5da-402cb0fc33c6)


#### Часть 2. Проверка назначения адреса SLAAC от R1

В части 2 вы убедитесь, что узел PC-A получает адрес IPv6 с помощью метода SLAAC.

Включите PC-A и убедитесь, что сетевой адаптер настроен для автоматической настройки IPv6.

Через несколько минут результаты команды ipconfig должны показать, что PC-A присвоил себе адрес из сети 2001:db8:1::/64.

![image](https://github.com/user-attachments/assets/ccfec168-c674-46e5-9938-73a293870f75)


Откуда взялась часть адреса с идентификатором хоста? **Была сгенерирована на основе MAC-адреса хоста**

## Часть 3. Настройка и проверка сервера DHCPv6 на R1

В части 3 выполняется настройка и проверка состояния DHCP-сервера на R1. Цель состоит в том, чтобы предоставить PC-A информацию о DNS-сервере и домене.

**Шаг 1.Более подробно изучите конфигурацию PC-A.**

Изучили вывод ipconfig /all. Подтверждаю отсутствие DNS суффикса. (Хотя, если бы мы модернизировали схему из части 1, то у нас бы сохранился DNS суффикс, который получали от DHCP v4)

**Шаг 2. Настройте R1 для предоставления DHCPv6 без состояния для PC-A.**

a. Создайте пул DHCP IPv6 на R1 с именем R1-STATELESS. В составе этого пула назначьте адрес DNS-сервера как 2001:db8:acad: :1, а имя домена — как stateless.com.

R1(config)# ipv6 dhcp pool R1-STATELESS

R1(config-dhcp)# dns-server 2001:db8:acad::254

R1(config-dhcp)# domain-name STATELESS.com

b. Настройте интерфейс G0/0/1 на R1, чтобы предоставить флаг конфигурации OTHER для локальной сети R1 и укажите только что созданный пул DHCP в качестве ресурса DHCP для этого интерфейса.

R1(config)# interface g0/0/1

R1(config-if)# ipv6 nd other-config-flag 

R1(config-if)# ipv6 dhcp server R1-STATELESS

c. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

d. Перезапустите PC-A.

e. Проверьте вывод ipconfig /all и обратите внимание на изменения.

f. Тестирование подключения с помощью пинга IP-адреса интерфейса E0/1 R2.


Поскольку мы настроили DHCP Pool с заданным DNS суффиксом, то после обновления настроек мыполучили адрес DNS и суффикс.

#### Часть 4. Настройка сервера DHCPv6 с сохранением состояния на R1

a. Создайте пул DHCPv6 на R1 для сети 2001:db8:acad:3:aaa::/80. Это предоставит адреса локальной сети, подключенной к интерфейсу E 0/1 на R2. В составе пула задайте DNS-сервер 2001:db8:acad: :254 и задайте доменное имя STATEFUL.com.

b. Назначьте только что созданный пул DHCPv6 интерфейсу E 0/0 на R1.

![image](https://github.com/user-attachments/assets/ee2fd387-a2c8-406c-b45b-02455fa4e2e3)


#### Настройка и проверка ретрансляции DHCPv6 на R2.

**Шаг 1. Включите PC-B и проверьте адрес SLAAC, который он генерирует.**

![image](https://github.com/user-attachments/assets/d7732023-7fa3-4e59-b7d4-17c1d5bb0d48)


**Шаг 2. Настройте R2 в качестве агента DHCP-ретрансляции для локальной сети на E 0/1.**

a. Настройте команду ipv6 dhcp relay на интерфейсе R2 E 0/1, указав адрес назначения интерфейса E 0/0 на R1. Также настройте команду managed-config-flag .

R2(config-if)# int E0/1
R2(config-if)# ipv6 nd managed-config-flag
R2(config-if)# ipv6 dhcp relay destination 2001:db8:acad:2::1 E0/0

![image](https://github.com/user-attachments/assets/cdc8e81d-667c-49bf-9456-ae0bb8ac8f74)

b. Сохраните конфигурацию.

**Шаг 3. Попытка получить адрес IPv6 из DHCPv6 на PC-B.**

a. Перезапустите PC-B.

b. Откройте командную строку на PC-B и выполните команду ipconfig /all и проверьте выходные данные, чтобы увидеть результаты операции ретрансляции DHCPv6.

С. Проверьте подключение с помощью пинга IP-адреса интерфейса R0 E 0/1.

![image](https://github.com/user-attachments/assets/7af8a3da-3962-4144-91b1-5849b2e51399)





