# Лабораторная работа - Внедрение маршрутизации между виртуальными локальными сетями

**Топология**

![image](https://github.com/DowningSun/OTUS/assets/156109695/b3502e16-befa-4024-9863-cf43a635bd45)

**Таблица адресации**

| Устройство | Интерфейс | IP-адрес | Маска подсети | Шлюз по умолчанию 
|:------------ |:---------------:| :-----: | :----------: | --------: |
|R1 |G0/0/1.10| 192.168.10.1 | 255.255.255.0 |  |
| | G0/0/1.20 | 192.168.20.1 | 255.255.255.0 |  |
| | G0/0/1.30 | 192.168.30.1 | 255.255.255.0 | |
| | G0/0/1.1000 | | ||
| S1 | VLAN 10 | 192.168.10.11 | 255.255.255.0 | 192.168.10.1 |
| S2 | VLAN 10 | 192.168.10.12 | 255.255.255.0 | 192.168.10.1 |
| PC-A |NIC| 192.168.20.3 | 255.255.255.0 | 192.168.20.1 |
|PC-B |NIC | 192.168.30.3 | 255.255.255.0 | 192.168.30.1 |

**Таблица VLAN**

| VLAN | Имя | Назначенный интерфейс
|:------- |:------: |:------:|
|10 |Управление |S1: VLAN 10 S2: VLAN 10|
|20 |Sales |S1: F0/6|
|30 |Operations |S2: F0/18|
|999 |Parking_Lot |С1: F0/2-4, F0/7-24, G0/1-2 С2: F0/2-17, F0/19-24, G0/1-2|
|1000 |Собственная ||

Задачи

Часть 1. Создание сети и настройка основных параметров устройства

Часть 2. Создание сетей VLAN и назначение портов коммутатора

Часть 3. Настройка транка 802.1Q между коммутаторами.

Часть 4. Настройка маршрутизации между сетями VLAN

Часть 5. Проверка, что маршрутизация между VLAN работает

#### Ход работы:

#### Часть 1 

**Создание сети и настройка основных параметров устройства**

**Шаг 2. Настройте базовые параметры для маршрутизатора.**

a. Подключитесь к маршрутизатору с помощью консоли и активируйте привилегированный режим EXEC.
Откройте окно конфигурации
               
b. Войдите в режим конфигурации.

c. Назначьте маршрутизатору имя устройства.

d. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

e. Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

f. Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

g. Установите cisco в качестве пароля виртуального терминала и активируйте вход.

h. Зашифруйте открытые пароли.

i. Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

j. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

k. Настройте на маршрутизаторе время.

![R1 Preconf](https://github.com/DowningSun/OTUS/assets/156109695/35835045-8302-425b-9743-763be75a5e9e)

**Шаг 2. Настройте базовые параметры каждого коммутатора.**

a. Присвойте коммутатору имя устройства.

b. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c. Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d. Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e. Установите cisco в качестве пароля виртуального терминала и активируйте вход.

f. Зашифруйте открытые пароли.

g. Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h. Настройте на коммутаторах время.

i. Сохранение текущей конфигурации в качестве начальной.

Закройте окно настройки.

![S1 S2 preconf](https://github.com/DowningSun/OTUS/assets/156109695/a7f595d4-b0ff-4b4c-a322-d1b1bbfeab78)

**Шаг 3. Настройте узлы ПК.**

Это было сложно, но мы справились.

#### Часть 2. Создание сетей VLAN и назначение портов коммутатора

Во второй части вы создадите VLAN, как указано в таблице выше, на обоих коммутаторах. Затем вы назначите VLAN соответствующему интерфейсу и проверите настройки конфигурации. Выполните следующие задачи на каждом коммутаторе.

**Шаг 1. Создайте сети VLAN на коммутаторах.**

a. Создайте и назовите необходимые VLAN на каждом коммутаторе из таблицы выше.

b. Настройте интерфейс управления и шлюз по умолчанию на каждом коммутаторе, используя информацию об IP-адресе в таблице адресации. 

c. Назначьте все неиспользуемые порты коммутатора VLAN Parking_Lot, настройте их для статического режима доступа и административно деактивируйте их.
Примечание. Команда interface range полезна для выполнения этой задачи с минимальным количеством команд.

![VLAN Creation](https://github.com/DowningSun/OTUS/assets/156109695/4bc1268e-c9b0-4cde-87d2-55b4d189c32c)


**Шаг 2. Назначьте сети VLAN соответствующим интерфейсам коммутатора.**

a. Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.

b. Убедитесь, что VLAN назначены на правильные интерфейсы.

![Vlan br](https://github.com/DowningSun/OTUS/assets/156109695/1e93896c-9a83-4f35-b5da-308b09c535fc)

#### Часть 3. Конфигурация магистрального канала стандарта 802.1Q между коммутаторами

**Шаг 1. Вручную настройте магистральный интерфейс F0/1 на коммутаторах S1 и S2.**

a. Настройка статического транкинга на интерфейсе F0/1 для обоих коммутаторов.

b. Установите native VLAN 1000 на обоих коммутаторах.

c. Укажите, что VLAN 10, 20, 30 и 1000 могут проходить по транку.

d. Проверьте транки, native VLAN и разрешенные VLAN через транк.

![Vlan trunk](https://github.com/DowningSun/OTUS/assets/156109695/2910ebd4-a7ef-4d87-9fa8-d6dcb3187eab)

#### Часть 4. Настройка маршрутизации между сетями VLAN
**Шаг 1. Настройте маршрутизатор.**

a. При необходимости активируйте интерфейс G0/0/1 на маршрутизаторе.

b. Настройте подинтерфейсы для каждой VLAN, как указано в таблице IP-адресации. Все подинтерфейсы используют инкапсуляцию 802.1Q. Убедитесь, что подинтерфейсу для native VLAN не назначен IP-адрес. Включите описание для каждого подинтерфейса.

c. Убедитесь, что вспомогательные интерфейсы работают

![R1 vlans](https://github.com/DowningSun/OTUS/assets/156109695/4d5524de-47a1-4e05-9db1-08f7a5e2f031)

#### Часть 5. Проверьте, работает ли маршрутизация между VLAN
**Шаг 1. Выполните следующие тесты с PC-A. Все должно быть успешно.**

a. Отправьте эхо-запрос с PC-A на шлюз по умолчанию.

b. Отправьте эхо-запрос с PC-A на PC-B.

c. Отправьте команду ping с компьютера PC-A на коммутатор S2.

**Шаг 2. Пройдите следующий тест с PC-B***

В окне командной строки на PC-B выполните команду tracert на адрес PC-A.

![test](https://github.com/DowningSun/OTUS/assets/156109695/55f76480-a903-42ff-a911-4d03f61f0817)

**Вопрос:Какие промежуточные IP-адреса отображаются в результатах?**

Промежуточным адлресом служил адрес шлюза (R1)