## Лабораторная работа. Настройка протокола OSPFv2 для одной области

**Торология**

![image](https://github.com/DowningSun/OTUS/assets/156109695/e2e7d00c-0c10-4884-8aeb-6a56a50f47d0)

**Таблица адресации**

| Устройство | Интерфейс | IP-адрес | Маска подсети |
| ------ | ------ | ------ | ------ |
| R1 | G0/0/1 | 10.53.0.1 | 255.255.255.0 |
| | Loopback1 | 172.16.1.1 | 255.255.255.0 |
| R2 | G0/0/1 | 10.53.0.2 | 255.255.255.0 |
| | Loopback1 | 192.168.1.1 | 255.255.255.0 |


#### Выполнение работы

       
**Часть 1. Создание сети и настройка основных параметров устройства**

         
**Шаг 1. Создайте сеть согласно топологии.**

Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

**Шаг 2. Произведите базовую настройку маршрутизаторов.**

a. Назначьте маршрутизатору имя устройства.

b. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c. Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d. Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e. Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

f. Зашифруйте открытые пароли.

g. Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

![R1 Preconf](https://github.com/DowningSun/OTUS/assets/156109695/56ee8622-b2fa-4e18-872b-5c654aa8562f)

**Шаг 3. Настройте базовые параметры каждого коммутатора.**

a. Назначьте коммутатору имя устройства.

b. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c. Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d. Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e. Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

f. Зашифруйте открытые пароли.

g. Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

![S1 S2 preconf](https://github.com/DowningSun/OTUS/assets/156109695/bf6b248f-a488-45e0-8582-6d312786d9c3)

                 

## Часть 2. Настройка и проверка базовой работы протокола OSPFv2 для одной области

**Шаг 1. Настройте адреса интерфейса и базового OSPFv2 на каждом маршрутизаторе.**

a. Настройте адреса интерфейсов на каждом маршрутизаторе, как показано в таблице адресации выше.

b. Перейдите в режим конфигурации маршрутизатора OSPF, используя идентификатор процесса 56.

c. Настройте статический идентификатор маршрутизатора для каждого маршрутизатора (1.1.1.1 для R1, 2.2.2.2 для R2).

d. Настройте инструкцию сети для сети между R1 и R2, поместив ее в область 0.

e. Только на R2 добавьте конфигурацию, необходимую для объявления сети Loopback 1 в область OSPF 0.

f. Убедитесь, что OSPFv2 работает между маршрутизаторами. Выполните команду, чтобы убедиться, что R1 и R2 сформировали смежность.
Вопрос:
Какой маршрутизатор является DR? Какой маршрутизатор является BDR? Каковы критерии отбора?
**R1 BDR, R2 DR
Для того чтобы выбрать для сети DR и BDR, маршрутизаторы просматривают значение приоритета в
hello-сообщениях:
Маршрутизатор с наивысшим значением приоритета становится DR.
Маршрутизатор со вторым наивысшим значением приоритета становится BDR.
По умолчанию приоритет интерфейса равен 1. Если у маршрутизаторов одинаковые приоритеты,
то DR и BDR выбираются по значению Router ID. Маршрутизатор с наивысшим Router ID
становится DR, а маршрутизатор со вторым наивысшим Router ID — BDR.**

g. На R1 выполните команду show ip route ospf, чтобы убедиться, что сеть R2 Loopback1 присутствует в таблице маршрутизации. Обратите внимание, что поведение OSPF по умолчанию заключается в объявлении интерфейса обратной связи в качестве маршрута узла с использованием 32-битной маски.

h. Запустите Ping до  адреса интерфейса R2 Loopback 1 из R1. Выполнение команды ping должно быть успешным.

![R1R2 OSPF CONF](https://github.com/DowningSun/OTUS/assets/156109695/cca467cb-f9df-4322-8371-abdd95068bac)


## Часть 3. Оптимизация и проверка конфигурации OSPFv2 для одной области

**Шаг 1. Реализация различных оптимизаций на каждом маршрутизаторе.**

a. На R1 настройте приоритет OSPF интерфейса G0/0/1 на 50, чтобы убедиться, что R1 является назначенным маршрутизатором.

b. Настройте таймеры OSPF на G0/0/1 каждого маршрутизатора для таймера приветствия, составляющего 30 секунд.

![R1R2 OSPF HELLO TIME](https://github.com/DowningSun/OTUS/assets/156109695/92bc14fa-ac41-4744-8547-2a56bf768405)

c. На R1 настройте статический маршрут по умолчанию, который использует интерфейс Loopback 1 в качестве интерфейса выхода. Затем распространите маршрут по умолчанию в OSPF. Обратите внимание на сообщение консоли после установки маршрута по умолчанию.

d. добавьте конфигурацию, необходимую для OSPF для обработки R2 Loopback 1 как сети точка-точка. Это приводит к тому, что OSPF объявляет Loopback 1 использует маску подсети интерфейса.

e. Только на R2 добавьте конфигурацию, необходимую для предотвращения отправки объявлений OSPF в сеть Loopback 1.

![R1R2 OSPF route+passive](https://github.com/DowningSun/OTUS/assets/156109695/6e3da19b-538e-42e2-ac1a-cd8d95ee5ceb)

f. Измените базовую пропускную способность для маршрутизаторов. После этой настройки перезапустите OSPF с помощью команды clear ip ospf process . Обратите внимание на сообщение консоли после установки новой опорной полосы пропускания.

![R1R2 autocost](https://github.com/DowningSun/OTUS/assets/156109695/db25f2dc-379c-44e6-b4d7-7b1a0f19527b)

**Шаг 2. Убедитесь, что оптимизация OSPFv2 реализовалась.**

a. Выполните команду show ip ospf interface g0/0/1 на R1 и убедитесь, что приоритет интерфейса установлен равным 50, а временные интервалы — Hello 30, Dead 120, а тип сети по умолчанию — Broadcast

b. На R1 выполните команду show ip route ospf, чтобы убедиться, что сеть R2 Loopback1 присутствует в таблице маршрутизации. Обратите внимание на разницу в метрике между этим выходным и предыдущим выходным. Также обратите внимание, что маска теперь составляет 24 бита, в отличие от 32 битов, ранее объявленных.

c. Введите команду show ip route ospf на маршрутизаторе R2. Единственная информация о маршруте OSPF должна быть распространяемый по умолчанию маршрут R1.

d. Запустите Ping до адреса интерфейса R1 Loopback 1 из R2. Выполнение команды ping должно быть успешным.

![R1R2 sh ip ro os](https://github.com/DowningSun/OTUS/assets/156109695/dbbdfc9b-6c01-42ee-9c63-b42fad8c3ce5)

Вопрос:
Почему стоимость OSPF для маршрута по умолчанию отличается от стоимости OSPF в R1 для сети 192.168.1.0/24?

**Количество хопов до шлюза по умолчанию больше, чем до порта-соседа, что увеличивает его стоимость**
