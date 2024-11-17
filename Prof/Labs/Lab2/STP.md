## Лабораторная работа. Развертывание коммутируемой сети с резервными каналами

#### Цели
Часть 1. Создание сети и настройка основных параметров устройства
Часть 2. Выбор корневого моста
Часть 3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов
Часть 4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов

#### Топология 

![TOP](https://github.com/DowningSun/OTUS/assets/156109695/75443cd8-486b-48a8-961e-8b367aa3562e)

#### Таблица адресации

| Устройство | Интерфейс | IP-адрес | Маска подсети |
| -------- | -------- | -------- | --------|
| S1 | Vlan 1 | 192.168.1.1 | 255.255.255.0 |
| S2 | Vlan 1 | 192.168.1.2 | 255.255.255.0 |
| S3 | Vlan 1 | 192.168.1.3 | 255.255.255.0 |

**Часть 1: Создание сети и настройка основных параметров устройства**

**Шаг 1: Создайте сеть согласно топологии.**

Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

**Шаг 2: Выполните инициализацию и перезагрузку коммутаторов.**

**Шаг 3: Настройте базовые параметры каждого коммутатора.**

a. Отключите поиск DNS.
b. Присвойте имена устройствам в соответствии с топологией.
c. Назначьте class в качестве зашифрованного пароля доступа к привилегированному режиму.
d. Назначьте cisco в качестве паролей консоли и VTY и активируйте вход для консоли и VTY каналов.
e. Настройте logging synchronous для консольного канала.
f. Настройте баннерное сообщение дня (MOTD) для предупреждения пользователей о запрете несанкционированного доступа.
g. Задайте IP-адрес, указанный в таблице адресации для VLAN 1 на всех коммутаторах.
h. Скопируйте текущую конфигурацию в файл загрузочной конфигурации.

![SW config](https://github.com/DowningSun/OTUS/assets/156109695/ad4110a3-e149-4495-b579-ec15945c356b)


**Шаг 4: Проверьте связь.**

Проверьте способность компьютеров обмениваться эхо-запросами.

Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S2?	**Успех**

Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S3?	**Успех**

Успешно ли выполняется эхо-запрос от коммутатора S2 на коммутатор S3?	**Успех**

Выполняйте отладку до тех пор, пока ответы на все вопросы не будут положительными.

![SW ping](https://github.com/DowningSun/OTUS/assets/156109695/8fef78c1-09f4-408f-b037-e0cc04ba67c5)

**Часть 2 Определение корневого моста**

Для каждого экземпляра протокола spanning-tree (коммутируемая сеть LAN или широковещательный домен) существует коммутатор, выделенный в качестве корневого моста. Корневой мост служит точкой привязки для всех расчётов протокола spanning-tree, позволяя определить избыточные пути, которые следует заблокировать.
Процесс выбора определяет, какой из коммутаторов станет корневым мостом. Коммутатор с наименьшим значением идентификатора моста (BID) становится корневым мостом. Идентификатор BID состоит из значения приоритета моста, расширенного идентификатора системы и MAC-адреса коммутатора. Значение приоритета может находиться в диапазоне от 0 до 65535 с шагом 4096. По умолчанию используется значение 32768.

**Шаг 1: Отключите все порты на коммутаторах.**

**Шаг 2: Настройте подключенные порты в качестве транковых.**

**Шаг 3: Включите порты F0/2 и F0/4 на всех коммутаторах.**

**Шаг 4: Отобразите данные протокола spanning-tree.**

![SW sh spa](https://github.com/DowningSun/OTUS/assets/156109695/a9e11da2-78c3-4519-b9b4-7b6ee729ccb8)

С учетом выходных данных, поступающих с коммутаторов, ответьте на следующие вопросы.
Какой коммутатор является корневым мостом? **S3 имеет статус This bridge is the root**

Почему этот коммутатор был выбран протоколом spanning-tree в качестве корневого моста?
**Корневым становится коммутатор с наименьшим идентификатором моста (Bridge ID). В случае одинаковых  BID происходит поиск устройства с "наименьшим" mac адресом**

Какие порты на коммутаторе являются корневыми портами? **S1: F0/2 ; S2:F 0/2**

Какие порты на коммутаторе являются назначенными портами? **S2: F0/4 ; S3: F0/2, F0/4**

Какой порт отображается в качестве альтернативного и в настоящее время заблокирован? **S1: F0/4**

Почему протокол spanning-tree выбрал этот порт в качестве невыделенного (заблокированного) порта?
**Состояние портов определяетьься по стоимости пути к корневому мосту. У представленного порта максимальная стоимость**

**Часть 3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов**

**Шаг 1. Определите коммутатор с заблокированным портом.**

**Шаг 2. Измените стоимость порта.**

![S1 cost](https://github.com/DowningSun/OTUS/assets/156109695/8f827a7d-92a0-40b0-ad16-f9d4ba2c3390)

**Шаг 3. Просмотрите изменения протокола spanning-tree.**

![S1S2 new cost](https://github.com/DowningSun/OTUS/assets/156109695/121daddc-3953-403e-95b1-d6aac4ccaec9)

Почему протокол spanning-tree заменяет ранее заблокированный порт на назначенный порт и блокирует порт, который был назначенным портом на другом коммутаторе?

**После изменения стоимости порта на S1 также изменилась суммартная стоимость пути к корневому коммутатору. В связи с этими изменениями протокол изменил и роли портов**

**Шаг 4. Удалите изменения стоимости порта.**

a. Выполните команду no spanning-tree vlan 1 cost 18 режима конфигурации интерфейса, чтобы удалить запись стоимости, созданную ранее.

b. Повторно выполните команду show spanning-tree, чтобы подтвердить, что протокол STP сбросил порт на коммутаторе некорневого моста, вернув исходные настройки порта. Протоколу STP требуется примерно 30 секунд, чтобы завершить процесс перевода порта.

![S1S2 no new cost](https://github.com/DowningSun/OTUS/assets/156109695/5aab3ba9-e5f3-4603-9794-67eb55a64593)

## Часть 4 Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов

a. Включите порты F0/1 и F0/3 на всех коммутаторах.
b. Подождите 30 секунд, чтобы протокол STP завершил процесс перевода порта, после чего выполните команду show spanning-tree на коммутаторах некорневого моста. Обратите внимание, что порт корневого моста переместился на порт с меньшим номером, связанный с коммутатором корневого моста, и заблокировал предыдущий порт корневого моста.

![S1S2 4 port](https://github.com/DowningSun/OTUS/assets/156109695/00e13d3c-b6fa-42ef-b188-c26cc17ae7df)

Какой порт выбран протоколом STP в качестве порта корневого моста на каждом коммутаторе некорневого моста? **S1: F0/1: S2: F0/1**
Почему протокол STP выбрал эти порты в качестве портов корневого моста на этих коммутаторах? 
**При прочих одинаковых условиях приоритет отдаётся порту с наименьшим порядковым номером**

## Вопросы для повторения

1. Какое значение протокол STP использует первым после выбора корневого моста, чтобы определить выбор порта?
2. Если первое значение на двух портах одинаково, какое следующее значение будет использовать протокол STP при выборе порта?
3. Если оба значения на двух портах равны, каким будет следующее значение, которое использует протокол STP при выборе порта?

**Порядок назначения ролей порта: 1) лучшая стоимость пути к корневому коммутатору 2) Лучшая стоимость в сегменте 3) Наименьший порядковый номер**



