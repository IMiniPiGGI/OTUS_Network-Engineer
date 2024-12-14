# Лабораторная работа - Внедрение маршрутизации между виртуальными локальными сетями

# Топология

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab06/pics/pic%20base.png)

# Таблица адресации

| Устройство | Интерфейс | IP-адрес | Маска подсети | Шлюз по умолчанию |
| --- | --- | --- | --- | --- |
| R1  | G0/0/1.10 | 192.168.10.1 | 255.255.255.0 | —   |
| R1  | G0/0/1.20 | 192.168.20.1 | 255.255.255.0 | —   |
| R1  | G0/0/1.30 | 192.168.30.1 | 255.255.255.0 | —   |
| R1  | G0/0/1.1000 | —   | —   | —   |
| S1  | VLAN 10 | 192.168.10.11 | 255.255.255.0 | 192.168.10.1 |
| S2  | VLAN 10 | 192.168.10.12 | 255.255.255.0 | 192.168.10.1 |
| PC-A | NIC | 192.168.20.3 | 255.255.255.0 | 192.168.20.1 |
| PC-B | NIC | 192.168.30.3 | 255.255.255.0 | 192.168.30.1 |

# Таблица VLAN

| VLAN | Имя | Назначенный интерфейс |
| --- | --- | --- |
| 10  | Управление | S1: VLAN 10<br><br>S2: VLAN 10 |
| 20  | Sales | S1: F0/6 |
| 30  | Operations | S2: F0/18 |
| 999 | Parking_Lot | С1: F0/2-4, F0/7-24, G0/1-2<br><br>С2: F0/2-17, F0/19-24, G0/1-2 |
| 1000 | Собственная | —   |

# Задачи

Часть 1. Создание сети и настройка основных параметров устройства

Часть 2. Создание сетей VLAN и назначение портов коммутатора

Часть 3. Настройка транка 802.1Q между коммутаторами.

Часть 4. Настройка маршрутизации между сетями VLAN

Часть 5. Проверка, что маршрутизация между VLAN работает

# Решение

## Создание сети и настройка основных параметров устройства

### Создайте сеть согласно топологии

Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab06/pics/pic%201.png)

### Настройте базовые параметры для маршрутизатора

1.  Подключитесь к маршрутизатору с помощью консоли и активируйте привилегированный режим EXEC.
2. Войдите в режим конфигурации.
3.  Назначьте маршрутизатору имя устройства.
4.  Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
5.  Назначьте **class** в качестве зашифрованного пароля привилегированного режима EXEC.
6.  Назначьте **cisco** в качестве пароля консоли и включите вход в систему по паролю.
7.  Установите **cisco** в качестве пароля виртуального терминала и активируйте вход.
8.  Зашифруйте открытые пароли.
9.  Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
10.  Сохраните текущую конфигурацию в файл загрузочной конфигурации.
11. Настройте на маршрутизаторе время.

```
Router>enable
Router#conf t
Enter configuration commands, one per line. End with CNTL/Z.
Router(config)#hostname R1
R1(config)#no ip domain-lookup
R1(config)#enable secret class
R1(config)#line con 0
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#logging syn
R1(config-line)#line vty 0 4
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#service password-encryption
R1(config)#banner log
R1(config)#banner login #UNAUTORIZED LOGING PROHIBITED!!!#
R1(config)#do wr
Building configuration...
[OK]
R1(config#)clock timezone Moscow 3
exit
R1#clock set 15:00 Dec 14 2024
R1#show clock
18:0:6.624 Moscow Sat Dec 14 2024
R1#wr
Building configuration...
[OK]

```

### Настройте базовые параметры каждого коммутатора

1. Присвойте коммутатору имя устройства.
2.  Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
3.  Назначьте **class** в качестве зашифрованного пароля привилегированного режима EXEC.
4.  Назначьте **cisco** в качестве пароля консоли и включите вход в систему по паролю.
5.  Установите **cisco** в качестве пароля виртуального терминала и активируйте вход.
6.  Зашифруйте открытые пароли.
7.  Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
8.  Настройте на коммутаторах время.
9.  Сохранение текущей конфигурации в качестве начальной.

```
Switch>en
Switch#conf t
Enter configuration commands, one per line. End with CNTL/Z.
Switch(config)#hostname S1
S1(config)#no ip domain-lookup
S1(config)#enable secret class
S1(config)#line con 0
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#logging syn
S1(config-line)#line vty 0 4
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#service pass
S1(config)#banner mo
S1(config)#banner motd #UNAUTORIZED ACCESS PROHIBITED!!!#
S1(config)#clock timezone Moscow 3 00
S1(config)#exit
S1#
%SYS-5-CONFIG_I: Configured from console by console
S1#clock set 15:07 14 Dec 2024
% Invalid input detected at '^' marker.
S1#show clock
18:7:4.647 Moscow Sat Dec 14 2024
S1#wr
Building configuration...
[OK]
```

### Настройте узлы ПК

Адреса ПК можно посмотреть в таблице адресации.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab06/pics/pic%202.png)

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab06/pics/pic%203.png)

## Создание сетей VLAN и назначение портов коммутатора

### Создайте сети VLAN на коммутаторах

1. Создайте и назовите необходимые VLAN на каждом коммутаторе из таблицы выше.
2. Настройте интерфейс управления и шлюз по умолчанию на каждом коммутаторе, используя информацию об IP-адресе в таблице адресации.
3.  Назначьте все неиспользуемые порты коммутатора VLAN Parking_Lot, настройте их для статического режима доступа и административно деактивируйте их.

S1
```
S1(config)#vlan 10
S1(config-vlan)#name Management
S1(config-vlan)#vlan 20
S1(config-vlan)#name Sales
S1(config-vlan)#vlan 30
S1(config-vlan)#name Operations
S1(config-vlan)#vlan 999
S1(config-vlan)#name Parking_Lot
S1(config-vlan)#vlan 1000
S1(config-vlan)#name Custom
S1(config-vlan)#exit
S1(config)#int vlan 10
S1(config-if)#ip add 192.168.10.11 255.255.255.0
S1(config-if)#no sh
S1(config-if)#ex
S1(config)#ip def
S1(config)#ip default-gateway 192.168.10.1
S1(config)#int ra F0/2-4,F0/7-24,G0/1-2
S1(config-if-range)#sw mode ac
S1(config-if-range)#sw mode access
S1(config-if-range)#sw ac vl 999
S1(config-if-range)#sh
S1(config-if-range)#do wr
Building configuration...
[OK]

```

S2

```

S2(config)#vlan 10
S2(config-vlan)#name Management
S2(config-vlan)#vlan 20
S2(config-vlan)#name Sales
S2(config-vlan)#vlan 30
S2(config-vlan)#name Operations
S2(config-vlan)#vlan 999
S2(config-vlan)#name Parking_Lot
S2(config-vlan)#vlan 1000
S2(config-vlan)#name Custom
S2(config-vlan)#ex
S2(config)#int vlan 10
S2(config-if)#ip add 192.168.10.12 255.255.255.0
S2(config-if)#no sh
S2(config-if)#ex
S2(config)#ip def 192.168.10.1
S2(config)#int range F0/2-17,F0/19-24,G0/1-2
S2(config-if-range)#sw m ac
S2(config-if-range)#sw ac vl 999
S2(config-if-range)#sh
S2(config-if-range)#do wr
Building configuration...
[OK]
```

### Назначьте сети VLAN соответствующим интерфейсам коммутатора

1. Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.
2.  Убедитесь, что VLAN назначены на правильные интерфейсы.

S1
```
S1(config)#int fa 0/6
S1(config-if)#sw m ac
S1(config-if)#sw ac vl 20
S1(config-if)#no sh
S1(config-if)#do wr
```
S2
```
S2(config)#int fa0/18
S2(config-if)#sw m ac
S2(config-if)#sw ac vl 30
S2(config-if)#no sh
S2(config-if)#do wr
Building configuration...
[OK]
```

## Конфигурация магистрального канала стандарта 802.1Q между коммутаторами

### Вручную настройте магистральный интерфейс F0/1 на коммутаторах S1 и S2
### Настройка статического транкинга на интерфейсе F0/1 для обоих коммутаторов.

1. Установите native VLAN 1000 на обоих коммутаторах.
2.  Укажите, что VLAN 10, 20, 30 и 1000 могут проходить по транку.
3.  Проверьте транки, native VLAN и разрешенные VLAN через транк.

```
S2(config)#int fa 0/1
S2(config-if)#switchport mode trunk
S2(config-if)#switchport trunk native vlan 1000
S2(config-if)# sw trunk allow vlan 10,20,30,1000
S2(config-if)#do wr
Building configuration...
[OK]
```

### Вручную настройте магистральный интерфейс F0/5 на коммутаторе S1

1. Настройте интерфейс S1 F0/5 с теми же параметрами транка, что и F0/1. Это транк до маршрутизатора.
2.  Сохраните текущую конфигурацию в файл загрузочной конфигурации.
3.  Проверка транкинга.

```
S1(config-if)#int fa 0/5
S1(config-if)#sw m tr
S1(config-if)#sw tr nat vl 1000
S1(config-if)#sw tr al vl 10,20,30,1000
S1(config-if)#do wr
Building configuration...
[OK]
```

#### Вопрос: Что произойдет, если G0/0/1 на R1 будет отключен?

#### Ответ: Ничего. Так как все устройства в разных vlan, никакой трафик не пойдёт. Единственное, что будет работать – vlan 10, так как оба устройства, находящиеся в нём будут находиться в одном vlan и в одной подсети.


## Настройка маршрутизации между сетями VLAN

### Настройте маршрутизатор

1. При необходимости активируйте интерфейс G0/0/1 на маршрутизаторе.
2. Настройте подинтерфейсы для каждой VLAN, как указано в таблице IP-адресации. Все подинтерфейсы используют инкапсуляцию 802.1Q. Убедитесь, что подинтерфейсу для native VLAN не назначен IP-адрес. Включите описание для каждого подинтерфейса.
3. Убедитесь, что вспомогательные интерфейсы работают

```
R1(config)#int gi 0/0/1
R1(config-if)#no sh
R1(config-if)#ex
R1(config)#int gi 0/0/1.10
R1(config-subif)#encapsulation dot1Q 10
R1(config-subif)# ip address 192.168.10.1 255.255.255.0
R1(config-subif)#int gi 0/0/1.20
R1(config-subif)#encapsulation dot1Q 20
R1(config-subif)#ip add 192.168.20.1 255.255.255.0
R1(config-subif)#int gi 0/0/1.30
R1(config-subif)#enc dot1Q 30
R1(config-subif)#ip add 192.168.30.1 255.255.255.0
R1(config-subif)#int gi 0/0/1.1000
R1(config-subif)#encap dot1Q 1000 native
R1(config-subif)#desc Custom
R1(config-subif)#int gi 0/0/1.10
R1(config-subif)#desc Management
R1(config-subif)#int gi 0/0/1.20
R1(config-subif)#desc Sales
R1(config-subif)#int gi 0/0/1.30
R1(config-subif)#desc Operations
```
## Проверьте, работает ли маршрутизация между VLAN

### Выполните следующие тесты с PC-A. Все должно быть успешно

1. Отправьте эхо-запрос с PC-A на шлюз по умолчанию.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab06/pics/pic%204.png)
2. Отправьте эхо-запрос с PC-A на PC-B.
![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab06/pics/pic%205.png)
3. Отправьте команду ping с компьютера PC-A на коммутатор S2.
![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab06/pics/pic%206.png)

### Пройдите следующий тест с PC-B

В окне командной строки на PC-B выполните команду **tracert** на адрес PC-A.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab06/pics/pic%207.png)

#### Вопрос: Какие промежуточные IP-адреса отображаются в результатах?

#### Ответ: 192.168.30.1 – адрес шлюза, 192.68.20.3 – адрес PC-A
