# Лабораторная работа - Реализация DHCPv4

## Топология

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab08/pics/pic_ipv4_base.png)

## Таблица адресации

| Устройство | Интерфейс | IP-адрес | Маска подсети | Шлюз по умолчанию |
| --- | --- | --- | --- | --- |
| R1  | G0/0/0 | 10.0.0.1 | 255.255.255.252 | —   |
| R1  | G0/0/1 |     | —   | —   |
| R1  | G0/0/1.100 | _192.168.1.1_ | _255.255.255.192_ | —   |
| R1  | G0/0/1.200 | _192.168.1.65_ | _255.255.255.224_ | —   |
| R1  | G0/0/1.1000 | —   | —   | —   |
| R2  | G0/0 | 10.0.0.2 | 255.255.255.252 | —   |
| R2  | G0/0/1 | _192.168.1.97_ | _255.255.255.240_ | —   |
| S1  | VLAN 200 | _192.168.1.66_ | _255.255.255.224_ | _192.168.1.65_ |
| S2  | VLAN 1 | _192.168.1.2_ | _255.255.255.192_ | _192.168.1.1_ |
| PC-A | NIC | DHCP | DHCP | DHCP |
| PC-B | NIC | DHCP | DHCP | DHCP |

## Таблица VLAN

| VLAN | Имя | Назначенный интерфейс |
| --- | --- | --- |
| 1   | Нет | S2: F0/18 |
| 100 | Клиенты | S1: F0/6 |
| 200 | Управление | S1: VLAN 200 |
| 999 | Parking_Lot | S1: F0/1-4, F0/7-24, G0/1-2 |
| 1000 | Собственная | —   |

# Задачи

Часть 1. Создание сети и настройка основных параметров устройства

Часть 2. Настройка и проверка двух серверов DHCPv4 на R1

Часть 3. Настройка и проверка DHCP-ретрансляции на R2

# Решение

## Часть 1. Создание сети и настройка основных параметров устройства

### Шаг. 1Создание схемы адресации

Подсеть сети 192.168.1.0/24 в соответствии со следующими требованиями:

1. Одна подсеть «Подсеть A», поддерживающая 58 хостов (клиентская VLAN на R1).

Подсеть A

Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.100 .

1. Одна подсеть «Подсеть B», поддерживающая 28 хостов (управляющая VLAN на R1).

Подсеть B:

Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.200. Запишите второй IP-адрес в таблице адресов для S1 VLAN 200 и введите соответствующий шлюз по умолчанию.

1. Одна подсеть «Подсеть C», поддерживающая 12 узлов (клиентская сеть на R2).

Подсеть C:

Запишите первый IP-адрес в таблице адресации для R2 G0/0/1.

Ответ: полученные значения записал в таблице адресации и выделили их курсивом. Так же, видимо в условии был пропущен рассчёт адреса для vlan 1, взял за адрес для vlan 1 второй адрес подсети A, опираясь на оригинальное задание по рассчёту от Cisco

### Шаг 2. Создайте сеть согласно топологии

Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab08/pics/pic_ipv4_1.png)

### Шаг 3. Произведите базовую настройку маршрутизаторов

1. Назначьте маршрутизатору имя устройства.
2. Откройте окно конфигурации
3. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
4. Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.
5. Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.
6. Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.
7. Зашифруйте открытые пароли.
8. Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
9. Сохраните текущую конфигурацию в файл загрузочной конфигурации.
10. Установите часы на маршрутизаторе на сегодняшнее время и дату.
```
Router>en
Router#conf t
Enter configuration commands, one per line. End with CNTL/Z.
Router(config)#hostname R1
R1(config)#no ip domain-lookup
Enter configuration commands, one per line. End with CNTL/Z.
R1(config)#enable secret class
R1(config)#line con 0
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#logging syn
R1(config-line)#line vty 0 4
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#banner motd #UNAUTORIZED ACCESS PROHIBITED!!!#
R1(config)#service pass
R1(config)#exit
R1#clock set 09:34 15 Dec 2024
R1#wr
```
### Шаг 4. Настройка маршрутизации между сетями VLAN на маршрутизаторе R1

1. Активируйте интерфейс G0/0/1 на маршрутизаторе.
2. Настройте подинтерфейсы для каждой VLAN в соответствии с требованиями таблицы IP-адресации. Все субинтерфейсы используют инкапсуляцию 802.1Q и назначаются первый полезный адрес из вычисленного пула IP-адресов. Убедитесь, что подинтерфейсу для native VLAN не назначен IP-адрес. Включите описание для каждого подинтерфейса.
3. Убедитесь, что вспомогательные интерфейсы работают.
```
R1#conf t
R1(config)#int gi 0/0/1
R1(config-if)#no sh
R1(config-if)#int gi 0/0/1.100
R1(config-subif)#encapsulation dot1Q 100
R1(config-subif)#ip addr 192.168.1.1 255.255.255.192
R1(config-subif)#int gi 0/0/1.200
R1(config-subif)#enc dot1Q 200
R1(config-subif)#ip addr 192.168.1.65 255.255.255.224
R1(config-subif)#int gi 0/0/1.1000
R1(config-subif)#enc dot1Q 1000 native
R1(config-subif)#no ip address
```
### Шаг 5. Настройте G0/1 на R2, затем G0/0/0 и статическую маршрутизацию для обоих маршрутизаторов

1. Настройте G0/0/1 на R2 с первым IP-адресом подсети C, рассчитанным ранее.
2. Настройте интерфейс G0/0/0 для каждого маршрутизатора на основе приведенной выше таблицы IP-адресации.
3. Настройте маршрут по умолчанию на каждом маршрутизаторе, указываемом на IP-адрес G0/0/0 на другом маршрутизаторе.
4. Убедитесь, что статическая маршрутизация работает с помощью пинга до адреса G0/0/1 R2 от R1.
5. Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```
R2#conf t
R2(config)#int gi 0/0/1
R2(config-if)#ip addr 192.168.1.97 255.255.255.240
R2(config-if)#no sh
R2(config-if)#int gi 0/0/0
R2(config-if)#ip addr 10.0.0.2 255.255.255.252
R2(config-if)#no sh
R2(config-if)#exit
R2(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.1
R2(config)#do wr
R1(config)#do ping 192.168.1.97
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/1 ms
```
### Шаг 6. Настройте базовые параметры каждого коммутатора

1. Присвойте коммутатору имя устройства.
2. Откройте окно конфигурации
3. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
4. Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.
5. Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.
6. Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.
7. Зашифруйте открытые пароли.
8. Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
9. Сохраните текущую конфигурацию в файл загрузочной конфигурации.
10. Установите часы на маршрутизаторе на сегодняшнее время и дату.
11. Скопируйте текущую конфигурацию в файл загрузочной конфигурации.
```
Switch#en
Switch#conf t
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
S1(config)#banner motd #UNAUTORIZED ACCESS PROHIBITED!!!#
S1(config)#service pass
S1(config)#exit
S1#clock set 09:34 15 Dec 2024
S1#copy running-config startup-config
Destination filename \[startup-config\]?
Building configuration...
[OK]
```
### Шаг 7. Создайте сети VLAN на коммутаторе S1

Примечание. S2 настроен только с базовыми настройками.

1. Создайте необходимые VLAN на коммутаторе 1 и присвойте им имена из приведенной выше таблицы.
2. Настройте и активируйте интерфейс управления на S1 (VLAN 200), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того установите шлюз по умолчанию на S1.
3. Настройте и активируйте интерфейс управления на S2 (VLAN 1), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того, установите шлюз по умолчанию на S2
4. Назначьте все неиспользуемые порты S1 VLAN Parking_Lot, настройте их для статического режима доступа и административно деактивируйте их. На S2 административно деактивируйте все неиспользуемые порты.

### S1
```
S1#conf t
S1(config)#vlan 100
S1(config-vlan)#name Clients
S1(config-vlan)#vlan 200
S1(config-vlan)#name Management
S1(config-vlan)#vlan 999
S1(config-vlan)#name Parking_Lot
S1(config-vlan)#vlan 1000
S1(config-vlan)#name Custom
S1(config-vlan)#exit
S1(config)#int vlan 200
S1(config-if)#ip addr 192.168.1.66 255.255.255.224
S1(config-if)#no sh
S1(config-if)#ex
S1(config)#ip default-gateway 192.168.1.65
S1(config)#int range f0/1-4,f0/7-24,gi0/1-2
S1(config-if-range)#sw m ac
S1(config-if-range)#sw ac vl 999
S1(config-if-range)#sh
```
### S2
```
S2#conf t
S2(config)#int vlan 1
S2(config-if)#ip addr 192.168.1.2 255.255.255.192
S2(config-if)#no sh
S2(config-if)#ex
S2(config)#ip default-gateway 192.168.1.1
S2(config)#
S2(config)#int range f0/1-4,f0/6-17,f0/19-24,gi0/1-2
S2(config-if-range)#sh
```
### Шаг 8. Назначьте сети VLAN соответствующим интерфейсам коммутатора

1. Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.
2. Убедитесь, что VLAN назначены на правильные интерфейсы.
```
S1(config)#int f0/6
S1(config-if)#sw m ac
S1(config-if)#sw ac vl 100
S2(config)#int fa 0/18
S2(config-if)#sw m ac
S2(config-if)#sw ac vl 1
```
Вопрос: Почему интерфейс F0/5 указан в VLAN 1?

Ответ: VLAN 1 является стандартным VLAN на многих коммутаторах Cisco и назначается обычно по умолчанию всем интерфейсам, если для них не указано иное

### Шаг 9. Вручную настройте интерфейс S1 F0/5 в качестве транка 802.1Q

1. Измените режим порта коммутатора, чтобы принудительно создать магистральный канал.
2. В рамках конфигурации транка установите для native VLAN значение 1000.
3. В качестве другой части конфигурации магистрали укажите, что VLAN 100, 200 и 1000 могут проходить по транку.
4. Сохраните текущую конфигурацию в файл загрузочной конфигурации.
5. Проверьте состояние транка.
```
S1(config)#int f0/5
S1(config-if)#switchport mode trunk
S1(config-if)#sw trunk native vlan 1000
S1(config-if)#sw trunk allowed vlan 100,200,1000
S1(config-if)#do wr
```
Вопрос: Какой IP-адрес был бы у ПК, если бы он был подключен к сети с помощью DHCP?

Ответ: 192.168.1.62, так как в циско раздача ip начинается с конца

## Часть 2. Настройка и проверка двух серверов DHCPv4 на R1

### Шаг 1. Настройте R1 с пулами DHCPv4 для двух поддерживаемых подсетей. Ниже приведен только пул DHCP для подсети A

1. Исключите первые пять используемых адресов из каждого пула адресов.
2. Создайте пул DHCP (используйте уникальное имя для каждого пула).
3. Укажите сеть, поддерживающую этот DHCP-сервер.
4. В качестве имени домена укажите CCNA-lab.com.
5. Настройте соответствующий шлюз по умолчанию для каждого пула DHCP.
6. Настройте время аренды на 2 дня 12 часов и 30 минут.
7. Затем настройте второй пул DHCPv4, используя имя пула R2_Client_LAN и вычислите сеть, маршрутизатор по умолчанию, и используйте то же имя домена и время аренды, что и предыдущий пул DHCP.

### Подсеть А
```
R1(config)#ip dhcp excluded-address 192.168.1.1 192.168.1.5
R1(config)#ip dhcp pool Subnet-A
R1(dhcp-config)#network 192.168.1.0 255.255.255.192
R1(dhcp-config)#domain-name CCNA-lab.com
R1(dhcp-config)#default-router 192.168.1.1
R1(dhcp-config)#lease 2 12 30
```
### Подсеть Б
```
R1(config)#ip dhcp excluded-address 192.168.1.65 192.168.1.70
R1(config)#ip dhcp pool Subnet-B
R1(dhcp-config)#network 192.168.1.64 255.255.255.224
R1(dhcp-config)#domain-name CCNA-lab.com
R1(dhcp-config)#default-router 192.168.1.65
R1(dhcp-config)#lease 2 12 30
```
### R2_Client_LAN
```
R1(config)#ip dhcp excluded-address 192.168.1.97 192.168.1.102
R1(config)#ip dhcp pool R2_Client_LAN
R1(dhcp-config)#network 192.168.1.96 255.255.255.240
R1(dhcp-config)#domain-name CCNA-lab.com
R1(dhcp-config)#default-router 192.168.1.97
R1(dhcp-config)#lease 2 12 30
```
К сожалению, в Cisco Packet Tracer на ISR 4321 я не нашёл возможности задавать период аренды, поэтому просто привёл команду, которая используется для этой цели.

### Шаг 2. Сохраните конфигурацию

Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```
R1#wr
Building configuration...
[OK]
```
### Шаг 3. Проверка конфигурации сервера DHCPv4

1. Чтобы просмотреть сведения о пуле, выполните команду show ip dhcp pool .

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab08/pics/pic_ipv4_2.png)

1. Выполните команду show ip dhcp bindings для проверки установленных назначений адресов DHCP.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab08/pics/pic_ipv4_3.png)

1. Выполните команду show ip dhcp server statistics для проверки сообщений DHCP.

В Cisco Packet Tracer на ISR 4321 нет возможности использования данной команды

### Шаг 3. Попытка получить IP-адрес от DHCP на PC-A

1. Из командной строки компьютера PC-A выполните команду ipconfig /all.
2. После завершения процесса обновления выполните команду ipconfig для просмотра новой информации об IP-адресе.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab08/pics/pic_ipv4_4.png)

1. Проверьте подключение с помощью пинга IP-адреса интерфейса R0 G0/0/1.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab08/pics/pic_ipv4_5.png)

## Часть 3. Настройка и проверка DHCP-ретрансляции на R2

### Шаг 1. Настройка R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/0/1

1. Настройте команду ip helper-address на G0/0/1, указав IP-адрес G0/0/0 R1.
2. Сохраните конфигурацию.
```
R2#conf t
R2(config-if)#ip helper-address 10.0.0.1
R2(config-if)#do wr
```
### Шаг 2. Попытка получить IP-адрес от DHCP на PC-B

1. Из командной строки компьютера PC-B выполните команду ipconfig /all.
2. После завершения процесса обновления выполните команду ipconfig для просмотра новой информации об IP-адресе.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab08/pics/pic_ipv4_6.png)

1. Проверьте подключение с помощью пинга IP-адреса интерфейса R1 G0/0/1.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab08/pics/pic_ipv4_7.png)

1. Выполните show ip dhcp binding для R1 для проверки назначений адресов в DHCP.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab08/pics/pic_ipv4_8.png)

1. Выполните команду show ip dhcp server statistics для проверки сообщений DHCP.

Как я уже писал ранее, в Cisco Packet Tracer на ISR 4321 нет возможности использования данной команды
