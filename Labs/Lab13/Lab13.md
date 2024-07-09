## Лабораторная работа - Настройка протоколов CDP, LLDP и NTP

Топология

![image](https://github.com/DowningSun/OTUS/assets/156109695/6df4c4da-8e46-42aa-8e65-c8317db479af)

Таблица адресации

| Устройство | Интерфейс | IP-адрес | Маска подсети | Шлюз по умолчанию |
| ------ | ------ | ------ | ------ | ------ |
| R1 | Loopback1 | 172.16.1.1 | 255.255.255.0 | ------ |
|    | G0/0/1 | 10.22.0.1 | 255.255.255.0 | ------ |
| S1 | SVI VLAN 1 | 10.22.0.2 | 255.255.255.0 | 10.22.0.1 |
| S2 | SVI VLAN 1 | 10.22.0.3 | 255.255.255.0 | 10.22.0.1 |

Задачи

Часть 1. Создание сети и настройка основных параметров устройства

Часть 2. Обнаружение сетевых ресурсов с помощью протокола CDP

Часть 3. Обнаружение сетевых ресурсов с помощью протокола LLDP

Часть 4. Настройка и проверка NTP

**Часть 1. Создание сети и настройка основных параметров устройства**

Шаг 1. Создайте сеть согласно топологии.

Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

Шаг 2. Настройте базовые параметры для маршрутизатора.

a. Назначьте маршрутизатору имя устройства.

b. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c. Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d. Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e. Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

f. Зашифруйте открытые пароли.

g. Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h. Настройка интерфейсов, перечисленных в таблице выше

i. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

**Шаг 3. Настройте базовые параметры каждого коммутатора.**

a. Присвойте коммутатору имя устройства.

b. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c. Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d. Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e. Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

f. Зашифруйте открытые пароли.

g. Создайте баннер, который предупреждает всех, кто обращается к устройству, видит баннерное сообщение «Только авторизованные пользователи!».  

h. Отключите неиспользуемые интерфейсы

i. Сохраните текущую конфигурацию в файл загрузочной конфигурации.



**Часть 2. Обнаружение сетевых ресурсов с помощью протокола CDP**

a. На R1 используйте соответствующую команду show cdp, чтобы определить, сколько интерфейсов включено CDP, сколько из них включено и сколько отключено.
 

**Вопрос:**

Сколько интерфейсов участвует в объявлениях CDP? Какие из них активны?

Введите ваш ответ здесь.

![image](https://github.com/DowningSun/OTUS/assets/156109695/d451071f-a7a6-42e3-b7d2-177453d5406f)

**Вывод sh cdp нам дал только информацию о настройках CDP, однако с помощью sh sdp int и sh cdp nei адулось собрать информацию о том, что в объявлениях участвуют VLAN1, G0/0/0-1. Vlan1 и G0/0/0/ выключен**

b. На R1 используйте соответствующую команду show cdp, чтобы определить версию IOS, используемую на S1.

![image](https://github.com/DowningSun/OTUS/assets/156109695/39b0fd6c-5b48-49f6-8ea3-03f25c333aba)

На S1 используйте соответствующую команду show cdp, чтобы определить, сколько пакетов CDP было выданных.

![image](https://github.com/DowningSun/OTUS/assets/156109695/390b17e6-7142-4291-9f49-0f5ac35fbe83)

**Сколько пакетов имеет выход CDP с момента последнего сброса счетчика?**

sh cdp TRAFFIC не поддерживается CPT.

Настройте SVI для VLAN 1 на S1 и S2, используя IP-адреса, указанные в таблице адресации выше. Настройте шлюз по умолчанию для каждого коммутатора на основе таблицы адресов.

e. На R1 выполните команду show cdp entry S1 . 

**Вопрос:**
Какие дополнительные сведения доступны теперь?

![image](https://github.com/DowningSun/OTUS/assets/156109695/a4d89e66-dc1b-439d-a89d-40ea3caf4a64)

Добавился IP-адрес S1

Отключить CDP глобально на всех устройствах. 


## Часть 3. Обнаружение сетевых ресурсов с помощью протокола LLDP

a. Введите соответствующую команду lldp, чтобы включить LLDP на всех устройствах в топологии.

b. На S1 выполните соответствующую команду lldp, чтобы предоставить подробную информацию о S2. 

![image](https://github.com/DowningSun/OTUS/assets/156109695/e6645c3c-7e70-406f-906c-85564da43825)

sh lldp entry недоступен в CPT.

**Что такое chassis ID  для коммутатора S2?**

В качестве Chassis ID используется MAC-адрес интерфейса управления

Соединитесь через консоль на всех устройствах и используйте команды LLDP, необходимые для отображения топологии физической сети только из выходных данных команды show.

![image](https://github.com/DowningSun/OTUS/assets/156109695/4849eb78-bf6e-424a-b8ef-30ca9978a284)

 
## Часть 4. Настройка NTP

Выведите на экран текущее время.

Откройте окно конфигурации

Введите команду show clock для отображения текущего времени на R1. Запишите отображаемые сведения о текущем времени в следующей таблице.

| Дата | Время | Часовой пояс | Источник |
| ------ | ------ | ------ | ------ |
| 1 mar 1993 | 2:5:29  | 141 UTC  | None |

С помощью команды clock set установите время на маршрутизаторе R1. Введенное время должно быть в формате UTC. 

Настройте главный сервер NTP.
Настройте R1 в качестве хозяина NTP с уровнем слоя 4.

**R1(config)#ntp master 4**

Настройте клиент NTP.

a. Выполните соответствующую команду на S1 и S2, чтобы просмотреть настроенное время. Запишите текущее время,  в следующей таблице.

| Дата | Время | Часовой пояс | Источник |
| ------ | ------ | ------ | ------ |
| 1 mar 1993 | 2:16:37  |  UTC  | None |
| 1 mar 1993 | 2:17:15  |  UTC  | None |

                 
Шаг 5. Проверьте настройку NTP.

a. Используйте соответствующую команду show , чтобы убедиться, что S1 и S2 синхронизированы с R1.

Примечание. Синхронизация метки времени на маршрутизаторе R2 с меткой времени на маршрутизаторе R1 может занять несколько минут.

b. Выполните соответствующую команду на S1 и S2, чтобы просмотреть настроенное время и сравнить ранее записанное время.

![image](https://github.com/DowningSun/OTUS/assets/156109695/d70e0afa-5537-4896-99cf-294b45c4b1d4)

**Вопрос для повторения**

Для каких интерфейсов в пределах сети не следует использовать протоколы обнаружения сетевых ресурсов? Поясните ответ.

На любых потенциально небезопасных портах, к которым может получить доступ злоумышленник и перехватить трафик. Например порт, ведущий с глобалььную или соседнюю сеть Т.к. передаётся информация об оборудовании, включая модель и адресацию.
