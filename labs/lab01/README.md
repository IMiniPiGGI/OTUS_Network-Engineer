Лабораторная работа. Базовая настройка коммутатора

# Топология

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/cc890a2972b0abac585d62647787bfa38239c69b/labs/lab01/pics/pic%20base.png)

# Таблица адресации

| Устройство | Интерфейс | IP-адрес / префикс |
| --- | --- | --- |
| S1  | VLAN 1 | 192.168.1.2 /24 |
| S1  | VLAN 99 |     |
| S1  | VLAN 99 |     |
| PC-A | NIC | 192.168.1.10 /24 |
| PC-A | NIC |     |
| PC-A | NIC |     |

# Задачи

Часть 1. Проверка конфигурации коммутатора по умолчанию

Часть 2. Создание сети и настройка основных параметров устройства

- Настройте базовые параметры коммутатора.
- Настройте IP-адрес для ПК.

Часть 3. Проверка сетевых подключений

- Отобразите конфигурацию устройства.
- Протестируйте сквозное соединение, отправив эхо-запрос.
- Протестируйте возможности удаленного управления с помощью Telnet.

# Решение

Часть 1

Шаг 1. Создаю сеть согласно топологии

1. Подсоединяю консольный кабель, как показано в топологии

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/cc890a2972b0abac585d62647787bfa38239c69b/labs/lab01/pics/pic%201.png)

1. Установлвиваю консольное подключение к коммутатору с компьютера PC-A с помощью эмуляции терминала

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/cc890a2972b0abac585d62647787bfa38239c69b/labs/lab01/pics/pic%202.png)

Вопрос: Почему нужно использовать консольное подключение для первоначальной настройки коммутатора? Почему нельзя подключиться к коммутатору через Telnet или SSH?

Ответ: Потому что изначально на устройстве нет настроенных сетевых интерфейсов, при помощи которых можно было бы установить соединение по протоколу TCP/IP

Шаг 2. Проверяю настройки коммутатора по умолчанию

1. Вывожу содержимое файла running-config, предварительно войдя в режим EXEC введя команду enable
````markdown
Switch>enable
Switch#show running-config
Building configuration...
Current configuration : 1080 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname Switch
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
!
interface FastEthernet0/2
!
interface FastEthernet0/3
!
interface FastEthernet0/4
!
interface FastEthernet0/5
!
interface FastEthernet0/6
!
interface FastEthernet0/7
!
interface FastEthernet0/8
!
interface FastEthernet0/9
!
interface FastEthernet0/10
!
interface FastEthernet0/11
!
interface FastEthernet0/12
!
interface FastEthernet0/13
!
interface FastEthernet0/14
!
interface FastEthernet0/15
!
interface FastEthernet0/16
!
interface FastEthernet0/17
!
interface FastEthernet0/18
!
interface FastEthernet0/19
!
interface FastEthernet0/20
!
interface FastEthernet0/21
!
interface FastEthernet0/22
!
interface FastEthernet0/23
!
interface FastEthernet0/24
!
interface GigabitEthernet0/1
!
interface GigabitEthernet0/2
!
interface Vlan1
 no ip address
 shutdown
!
line con 0
!
line vty 0 4
 login
line vty 5 15
 login
!
end
````

Вопрос: Сколько интерфейсов FastEthernet имеется на коммутаторе 2960?

Ответ: 24

Вопрос: Сколько интерфейсов Gigabit Ethernet имеется на коммутаторе 2960?

Ответ: 2

Вопрос: Каков диапазон значений, отображаемых в vty-линиях?

Ответ: 0-15

1. Вывожу содержимое файла startup-config
````
Switch#show startup-config
startup-config is not present
````
Вопрос: Почему появляется это сообщение?

Ответ: Потому что для устройства нет сохранённого файла конфигурации.

1. Вывожу свойства SVI VLAN 1
````
Switch#Show interface vlan 1
Vlan1 is administratively down, line protocol is down
Hardware is CPU Interface, address is 0002.4a3a.774b (bia 0002.4a3a.774b)
MTU 1500 bytes, BW 100000 Kbit, DLY 1000000 usec,
reliability 255/255, txload 1/255, rxload 1/255
Encapsulation ARPA, loopback not set
ARP type: ARPA, ARP Timeout 04:00:00
Last input 21:40:21, output never, output hang never
Last clearing of "show interface" counters never
Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
Queueing strategy: fifo
Output queue: 0/40 (size/max)
5 minute input rate 0 bits/sec, 0 packets/sec
5 minute output rate 0 bits/sec, 0 packets/sec
1682 packets input, 530955 bytes, 0 no buffer
Received 0 broadcasts (0 IP multicast)
0 runts, 0 giants, 0 throttles
0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
563859 packets output, 0 bytes, 0 underruns
0 output errors, 23 interface resets
0 output buffer failures, 0 output buffers swapped out
````
Вопрос: Назначен ли IP-адрес сети VLAN 1?

Ответ: Нет

Вопрос: Какой MAC-адрес имеет SVI?

Ответ: 0002.4a3a.774b

Вопрос: Данный интерфейс включен?

Ответ: Нет

1. Вывожу IP-свойства интерфейса SVI сети VLAN 1 командой show ip interface vlan 1
````
Switch#show ip interface vlan 1
Vlan1 is administratively down, line protocol is down
Internet protocol processing disabled
````
Вопрос: Какие выходные данные вы видите?

Ответ: Интерфейс выключен, протокол IP отключен.

1. Подсоединяю кабель Ethernet компьютера PC-A к порту 6 на коммутаторе и изучаю IP-свойства интерфейса SVI сети VLAN 1, предварительно дождавшись согласования параметров скорости и дуплекса между коммутатором и ПК

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/cc890a2972b0abac585d62647787bfa38239c69b/labs/lab01/pics/pic%203.png)
Вопрос: Какие выходные данные вы видите?

Ответ: Интерфейс выключен, протокол IP отключен.

1. Вывожу информацию о версии Cisco IOS командой show version
````
Switch#Show version
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)
Technical Support: <http://www.cisco.com/techsupport>
Copyright (c) 1986-2013 by Cisco Systems, Inc.
Compiled Wed 26-Jun-13 02:49 by mnguyen

ROM: Bootstrap program is C2960 boot loader
BOOTLDR: C2960 Boot Loader (C2960-HBOOT-M) Version 12.2(25r)FX, RELEASE SOFTWARE (fc4)

Switch uptime is 39 minutes
System returned to ROM by power-on
System image file is "flash:c2960-lanbasek9-mz.150-2.SE4.bin"

This product contains cryptographic features and is subject to United
States and local country laws governing import, export, transfer and
use. Delivery of Cisco cryptographic products does not imply
third-party authority to import, export, distribute or use encryption.
Importers, exporters, distributors and users are responsible for
compliance with U.S. and local country laws. By using this product you
agree to comply with applicable laws and regulations. If you are unable
to comply with U.S. and local laws, return this product immediately.

A summary of U.S. laws governing Cisco cryptographic products may be found at:
http://www.cisco.com/wwl/export/crypto/tool/stqrg.html

If you require further assistance please contact us by sending email to
export@cisco.com.

cisco WS-C2960-24TT-L (PowerPC405) processor (revision B0) with 65536K bytes of memory.
Processor board ID FOC1010X104
Last reset from power-on
1 Virtual Ethernet interface
24 FastEthernet interfaces
2 Gigabit Ethernet interfaces
The password-recovery mechanism is enabled.

64K bytes of flash-simulated non-volatile configuration memory.
Base ethernet MAC Address : 00:02:4A:3A:77:4B
Motherboard assembly number : 73-10390-03
Power supply part number : 341-0097-02
Motherboard serial number : FOC10093R12
Power supply serial number : AZS1007032H
Model revision number : B0
Motherboard revision number : B0
Model number : WS-C2960-24TT-L
System serial number : FOC1010X104
Top Assembly Part Number : 800-27221-02
Top Assembly Revision Number : A0
Version ID : V02
CLEI Code Number : COM3L00BRA
Hardware Board Revision Number : 0x01

Switch Ports Model           SW Version SW Image
------ ----- -----           ---------  ----------

*    1  26   WS-C2960-24TT-L 15.0(2)SE4 C2960-LANBASEK9-M

Configuration register is 0xF

Вопрос: Под управлением какой версии ОС Cisco IOS работает коммутатор?
````
Ответ: 15.0(2)SE4

Вопрос: Как называется файл образа системы?

Ответ: flash:c2960-lanbasek9-mz.150-2.SE4.bin

1. Вывожу свойства по умолчанию интерфейса FastEthernet, который используется компьютером PC-A командой show interface f0/6
````
Switch#show interface f0/6
FastEthernet0/6 is up, line protocol is up (connected)
Hardware is Lance, address is 000a.f345.ae06 (bia 000a.f345.ae06)
BW 100000 Kbit, DLY 1000 usec,
reliability 255/255, txload 1/255, rxload 1/255
Encapsulation ARPA, loopback not set
Keepalive set (10 sec)
Full-duplex, 100Mb/s
input flow-control is off, output flow-control is off
ARP type: ARPA, ARP Timeout 04:00:00
Last input 00:00:08, output 00:00:05, output hang never
Last clearing of "show interface" counters never
Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
Queueing strategy: fifo
Output queue :0/40 (size/max)
5 minute input rate 0 bits/sec, 0 packets/sec
5 minute output rate 0 bits/sec, 0 packets/sec
956 packets input, 193351 bytes, 0 no buffer
Received 956 broadcasts, 0 runts, 0 giants, 0 throttles
0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored, 0 abort
0 watchdog, 0 multicast, 0 pause input
0 input packets with dribble condition detected
2357 packets output, 263570 bytes, 0 underruns
0 output errors, 0 collisions, 10 interface resets
0 babbles, 0 late collision, 0 deferred
0 lost carrier, 0 no carrier
0 output buffer failures, 0 output buffers swapped out
````
Вопрос: Интерфейс включен или выключен?

Ответ: Включен

Вопрос: Что нужно сделать, чтобы включить интерфейс?

Ответ: Перейти в режим глобальной конфигурации терминала (conf t), затем перейти в режим конфигурации интерфейса (int fa0/6), после чего ввести no shutdown

Вопрос: Какой MAC-адрес у интерфейса?

Ответ: 000a.f345.ae06

Вопрос: Какие настройки скорости и дуплекса заданы в интерфейсе?

Ответ: Full-duplex, 100Mb/s

1. Вывожу информацию о флеш-памяти командой show flash (так же можно использовать dir flash:)
````
Switch#show flash
Directory of flash:/
1 -rw- 4670455 &lt;no date&gt; 2960-lanbasek9-mz.150-2.SE4.bin
64016384 bytes total (59345929 bytes free
````
Вопрос: Какое имя присвоено образу Cisco IOS?

Ответ: 2960-lanbasek9-mz.150-2.SE4.bin

Часть 2. Настраиваю базовые параметры сетевых устройств

Шаг 1. Настраиваю базовые параметры коммутатора

1. В режиме глобальной конфигурации на S1 вставляю следующие параметры:

no ip domain-lookup  
hostname S1  
service password-encryption  
enable secret class  
banner motd # Unauthorized access is strictly prohibited. #
````
Switch#conf t
Enter configuration commands, one per line. End with CNTL/Z.
Switch(config)#no ip domain-lookup
Switch(config)#hostname S1
S1(config)#service password-encryption
S1(config)#enable secret class
S1(config)#banner motd # Unauthorized access is strictly prohibited. #
````
1. Назначаю IP-адрес SVI на коммутаторе.
````
S1(config)#int vlan 1
S1(config-if)#ip address 192.168.1.2 255.255.255.0
````
1. Ограничиваю доступ через порт консоли с помощью пароля, использую пароль cisco в качестве пароля для входа в консоль в этом задании. Использую параметр logging synchronous, чтобы консольные сообщения не прерывали выполнение команд.
````
S1(config)#line console 0
S1(config-line)#pass line cisco
S1(config-line)#logging syn
````
1. Настраиваю каналы виртуального соединения для удалённого управления (vty), чтобы коммутатор разрешил подключение через telnet.
````
S1(config)#line vty 0 4
S1(config-line)#pass cisco
S1(config-line)#login
````
Вопрос: Для чего нужна команда login?

Ответ: Для настройки метода аутентификации.

Шаг 2. Настраиваю IP-адрес на компьютере PC-A.

1. Назначаю компьютеру IP-адрес и маску подсети в соответствии с таблицей адресации

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/cc890a2972b0abac585d62647787bfa38239c69b/labs/lab01/pics/pic%204.png)

Часть 3. Часть 3. Проверка сетевых подключений

Шаг 1. Вывожу конфигурацию коммутатора.

1. Вывод конфигурации терминала при помощи команды show run. Вижу что vlan 1 по-прежнему выключен и включаю его так же, как и любой другой интерфейс.
````
S1#show run
Building configuration...
Current configuration : 1296 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
!
hostname S1
!
enable secret 5 $1$mERr$9cTjUIEqNGurQiFU.ZeCi1
!
!
!
no ip domain-lookup
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
!
interface FastEthernet0/2
!
interface FastEthernet0/3
!
interface FastEthernet0/4
!
interface FastEthernet0/5
!
interface FastEthernet0/6
!
interface FastEthernet0/7
!
interface FastEthernet0/8
!
interface FastEthernet0/9
!
interface FastEthernet0/10
!
interface FastEthernet0/11
!
interface FastEthernet0/12
!
interface FastEthernet0/13
!
interface FastEthernet0/14
!
interface FastEthernet0/15
!
interface FastEthernet0/16
!
interface FastEthernet0/17
!
interface FastEthernet0/18
!
interface FastEthernet0/19
!
interface FastEthernet0/20
!
interface FastEthernet0/21
!
interface FastEthernet0/22
!
interface FastEthernet0/23
!
interface FastEthernet0/24
!
interface GigabitEthernet0/1
!
interface GigabitEthernet0/2
!
interface Vlan1
ip address 192.168.1.2 255.255.255.0
!
banner motd ^C Unauthorized access is strictly prohibited. ^C
!
!
!
line con 0
password 7 082D45400C59061E010803
logging synchronous
!
line vty 0 4
password 7 0822455D0A16
login
line vty 5 15
login
!
!
!
!
end
````
1. Проверяю конфигурацию VLAN 1
````
S1#Sh int vl 1
Vlan1 is administratively down, line protocol is down
Hardware is CPU Interface, address is 0002.4a3a.774b (bia 0002.4a3a.774b)
Internet address is 192.168.1.2/24
MTU 1500 bytes, BW 100000 Kbit, DLY 1000000 usec,
reliability 255/255, txload 1/255, rxload 1/255
Encapsulation ARPA, loopback not set
ARP type: ARPA, ARP Timeout 04:00:00
Last input 21:40:21, output never, output hang never
Last clearing of "show interface" counters never
Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
Queueing strategy: fifo
Output queue: 0/40 (size/max)
5 minute input rate 0 bits/sec, 0 packets/sec
5 minute output rate 0 bits/sec, 0 packets/sec
1682 packets input, 530955 bytes, 0 no buffer
Received 0 broadcasts (0 IP multicast)
0 runts, 0 giants, 0 throttles
0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
563859 packets output, 0 bytes, 0 underruns
0 output errors, 23 interface resets
0 output buffer failures, 0 output buffers swapped out
````
Вопрос: Какова полоса пропускания этого интерфейса?

Ответ: BW 100000 Kbit

Шаг 2. Тестирую сквозное соединение, отправив эхо-запрос

1. В командной строке компьютера PC-A с помощью утилиты ping проверяю связь сначала с адресом PC-A.
````
C:\>ping 192.168.1.10

Pinging 192.168.1.10 with 32 bytes of data:

Reply from 192.168.1.10: bytes=32 time=3ms TTL=128
Reply from 192.168.1.10: bytes=32 time<1ms TTL=128
Reply from 192.168.1.10: bytes=32 time=3ms TTL=128
Reply from 192.168.1.10: bytes=32 time=16ms TTL=128

Ping statistics for 192.168.1.10:
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
Minimum = 0ms, Maximum = 16ms, Average = 5ms
````
1. Из командной строки компьютера PC-A отправляю эхо-запрос на административный адрес интерфейса SVI коммутатора S1.
````
C:\>ping 192.168.1.2

Pinging 192.168.1.2 with 32 bytes of data:

Request timed out.
Reply from 192.168.1.2: bytes=32 time=1ms TTL=255
Reply from 192.168.1.2: bytes=32 time<1ms TTL=255
Reply from 192.168.1.2: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.1.2:
Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
Minimum = 0ms, Maximum = 1ms, Average = 0ms
````
Шаг 3. Шаг 3. Проверяю удаленное управление коммутатором S1.

1. Открываю программу эмуляции Telnet
2. Выбираю сервер telnet и указываю адрес управления SVI для подключения к S1 (Пароль cisco)

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/cc890a2972b0abac585d62647787bfa38239c69b/labs/lab01/pics/pic%205.png)

1. После ввода пароля cisco оказываюсь в командной строке пользовательского режима. Для перехода в исполнительский режим EXEC ввожу команду enable и использую секретный пароль class.
````
Trying 192.168.1.2 ...Open Unauthorized access is strictly prohibited.

User Access Verification

Password:
S1>enable
Password:
S1#
````
1. Сохраняю конфигурацию
````
S1#write memory

Building configuration...

[OK]
````
1. Ввожу exit, чтобы завершить сеанс telnet

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/cc890a2972b0abac585d62647787bfa38239c69b/labs/lab01/pics/pic%206.png)

Вопросы для повторения

1. Зачем необходимо настраивать пароль VTY для коммутатора?

Для того, чтобы была возможность подключения к коммутатору

1. Что нужно сделать, чтобы пароли не отправлялись в незашифрованном виде?

Нужно включить шифрование паролей командой service password-encryption
