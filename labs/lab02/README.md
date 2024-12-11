# Лабораторная работа. Просмотр таблицы MAC-адресов коммутатора

# Топология

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab02/pics/pic%20base.png)

# Таблица адресации

| Устройство | Интерфейс | IP-адрес | Маска подсети |
| --- | --- | --- | --- |
| S1  | VLAN 1 | 192.168.1.11 | 255.255.255.0 |
| S2  | VLAN 1 | 192.168.1.12 | 255.255.255.0 |
| PC-A | NIC | 192.168.1.1 | 255.255.255.0 |
| PC-B | NIC | 192.168.1.2 | 255.255.255.0 |

# Цели

Часть 1. Создание и настройка сети

Часть 2. Изучение таблицы МАС-адресов коммутатора

# Решение

## Часть 1. Создание и настройка сети

Шаг 1. Подключаю сеть, в соответствии с топологией

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab02/pics/pic%201.png)

Шаг 2. Настраиваю узлы ПК

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab02/pics/pic%202.png)

Шаг 3. Выполняю инициализацию и перезагрузку коммутаторов
```
Switch>enable
Switch#show flash
Directory of flash:/

1 -rw- 4670455 &lt;no date&gt; 2960-lanbasek9-mz.150-2.SE4.bin

64016384 bytes total (59345929 bytes free)
Switch#erase startup-config
Erasing the nvram filesystem will remove all configuration files! Continue? \[confirm\]y\[OK\]
Erase of nvram: complete
%SYS-7-NV_BLOCK_INIT: Initialized the geometry of nvram
Switch#reload
Proceed with reload? \[confirm\]yC2960 Boot Loader (C2960-HBOOT-M) Version 12.2(25r)FX, RELEASE SOFTWARE (fc4)
Cisco WS-C2960-24TT (RC32300) processor (revision C0) with 21039K bytes of memory.
2960-24TT starting...
Base ethernet MAC Address: 000D.BD3A.A9DB
Xmodem file system is available.
Initializing Flash...
flashfs\[0\]: 1 files, 0 directories
flashfs\[0\]: 0 orphaned files, 0 orphaned directories
flashfs\[0\]: Total bytes: 64016384
flashfs\[0\]: Bytes used: 4670455
flashfs\[0\]: Bytes available: 59345929
flashfs\[0\]: flashfs fsck took 1 seconds.
...done Initializing Flash.

Boot Sector Filesystem (bs:) installed, fsid: 3
Parameter Block Filesystem (pb:) installed, fsid: 4

Loading "flash:/2960-lanbasek9-mz.150-2.SE4.bin"...
########################################################################## \[OK\]
Smart Init is enabled
smart init is sizing iomem
TYPE MEMORY_REQ
TOTAL: 0x00000000
Rounded IOMEM up to: 0Mb.
Using 6 percent iomem. \[0Mb/512Mb\]

Restricted Rights Legend
Use, duplication, or disclosure by the Government is
subject to restrictions as set forth in subparagraph
(c) of the Commercial Computer Software - Restricted
Rights clause at FAR sec. 52.227-19 and subparagraph
(c) (1) (ii) of the Rights in Technical Data and Computer
Software clause at DFARS sec. 252.227-7013.
cisco Systems, Inc.
170 West Tasman Drive
San Jose, California 95134-1706
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)
Technical Support: <http://www.cisco.com/techsupport>
Copyright (c) 1986-2013 by Cisco Systems, Inc.
Compiled Wed 26-Jun-13 02:49 by mnguyen
Initializing flashfs...
fsck: Disable shadow buffering due to heap fragmentation.
flashfs\[2\]: 2 files, 1 directories
flashfs\[2\]: 0 orphaned files, 0 orphaned directories
flashfs\[2\]: Total bytes: 32514048
flashfs\[2\]: Bytes used: 11952128
flashfs\[2\]: Bytes available: 20561920
flashfs\[2\]: flashfs fsck took 2 seconds.
flashfs\[2\]: Initialization complete....done Initializing flashfs.
Checking for Bootloader upgrade..
Boot Loader upgrade not required (Stage 2)
POST: CPU MIC register Tests : Begin
POST: CPU MIC register Tests : End, Status Passed
POST: PortASIC Memory Tests : Begin
POST: PortASIC Memory Tests : End, Status Passed
POST: CPU MIC interface Loopback Tests : Begin
POST: CPU MIC interface Loopback Tests : End, Status Passed
POST: PortASIC RingLoopback Tests : Begin
POST: PortASIC RingLoopback Tests : End, Status Passed
POST: PortASIC CAM Subsystem Tests : Begin
POST: PortASIC CAM Subsystem Tests : End, Status Passed
POST: PortASIC Port Loopback Tests : Begin
POST: PortASIC Port Loopback Tests : End, Status Passed
Waiting for Port download...Complete

This product contains cryptographic features and is subject to United
States and local country laws governing import, export, transfer and
use. Delivery of Cisco cryptographic products does not imply
third-party authority to import, export, distribute or use encryption.
Importers, exporters, distributors and users are responsible for
compliance with U.S. and local country laws. By using this product you
agree to comply with applicable laws and regulations. If you are unable
to comply with U.S. and local laws, return this product immediately.
A summary of U.S. laws governing Cisco cryptographic products may be found at:
<http://www.cisco.com/wwl/export/crypto/tool/stqrg.html>
If you require further assistance please contact us by sending email to
<export@cisco.com>.
cisco WS-C2960-24TT-L (PowerPC405) processor (revision B0) with 65536K bytes of memory.
Processor board ID FOC1010X104
Last reset from power-on
1 Virtual Ethernet interface
24 FastEthernet interfaces
2 Gigabit Ethernet interfaces
The password-recovery mechanism is enabled.
64K bytes of flash-simulated non-volatile configuration memory.
Base ethernet MAC Address : 00:0D:BD:3A:A9:DB
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

Switch  Ports Model            SW Version     SW Image

------ ----- -----            ----------     ----------

*    1 26     WS-C2960-24TT-L 15.0(2)SE4     C2960-LANBASEK9-M

Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)
Technical Support: <http://www.cisco.com/techsupport>
Copyright (c) 1986-2013 by Cisco Systems, Inc.
Compiled Wed 26-Jun-13 02:49 by mnguyen

Press RETURN to get started!
```
Шаг 4. Настраиваю базовые параметры каждого коммутатора

1. Настраиваю имена в соответствии с топологией
```
Switch>en
Switch#conf t

Enter configuration commands, one per line. End with CNTL/Z.

Switch(config)#hostname S2
S2(config)#no ip domain-lookup
```
1. Настраиваю IP-адреса, как указано в таблице адресации
```
S2(config)#int vl 1
S2(config-if)#ip a 192.168.1.12 255.255.255.0
S2(config-if)#no sh
```
1. Назначаю cisco в качестве паролей консоли и vty
```
S1(config)#service password-encryption
S1(config)#line vty 0 4
S1(config-line)#pass cisco
S1(config-line)#login
S1(config-line)#line con 0
S1(config-line)#pass cisco
S1(config-line)#logging syn
```
1. Назначаю class в качестве пароля к привилегированному режиму EXEC

S1(config)#enable secret class

Часть 2. Изучаю таблицу MAC-адресов коммутатора

Шаг 1. Записываю MAC-адреса сетевых устройсв

1. Открываю командную строку на PC-A и PC-B и ввожу команду ipconfig /all

MAC-адрес компьютера PC-A: 0090.0C0D.B430

MAC-адрес компьютера PC-B: 0004.9A32.D26B

1. Подключаюсь к коммутаторам S1 и S2 через консоль и ввожу команду show interface F0/1 на каждом коммутаторе

МАС-адрес коммутатора S1 Fast Ethernet 0/1: 0060.2f8a.9a01

МАС-адрес коммутатора S2 Fast Ethernet 0/1: 0001.9654.8201

Шаг 2. Смотрю таблицу МАС-адресов коммутатора

1. Подключаюсь к коммутатору S2 через консоль и вхожу в привилегированный режим EXEC
2. В привилегированном режиме EXEC ввожу команду show mac address-table и нажимаю клавишу ввода.
```
S2#show mac address-table

Mac Address Table
-------------------------------------------

Vlan  Mac Address    Type     Ports

---- -----------    -------- -----
   1  0060.2f8a.9a01 DYNAMIC  Fa0/1
```
Вопрос: Записаны ли в таблице МАС-адресов какие-либо МАС-адреса?

Ответ: Да

Вопрос: Какие МАС-адреса записаны в таблице? С какими портами коммутатора они сопоставлены и каким устройствам принадлежат?

Ответ: В таблице записан MAC-адрес соседнего коммутатора. Сопоставлен с портом Fa0/1 и принадлежит коммутатору S1

Вопрос: Если вы не записали МАС-адреса сетевых устройств в шаге 1, как можно определить, каким устройствам принадлежат МАС-адреса, используя только выходные данные команды show mac address-table? Работает ли это решение в любой ситуации?

Ответ: При помощи команды show mac address-table можно определить, к какому порту подключено устройство, после чего можно либо по физике, либо по схеме (при ей наличии) посмотреть, какое устройство подключено к этому порту коммутатора. Так же можно узнать, какое устройство подключено к этому порту, если есть предстваление, какому устройству принадлежит mac-адрес. На мой взгляд, это решение работает в редких ситуациях и предполагает наличие большого объёма вводной информации и простоты инфраструктуры. В реальности, вывод mac address-table эффективнее использовать в комплексе show cdp neighbors и show arp. Так же хорошей привычкой будет подпись портов при пощи description, тогда, в целом, будет достаточно вывода show mac address-table

Шаг 3. Очищаю таблицу МАС-адресов коммутатора S2 и снова отображаю таблицу МАС-адресов.

1. В привилегированном режиме EXEC введите команду clear mac address-table dynamic и нажмите клавишу Enter.
```
S2#clear mac address-table dynamic
```
1. Снова быстро ввожу команду show mac address-table
```
S2#sh mac add

Mac Address Table
\-------------------------------------------

Vlan Mac Address Type Ports
\---- ----------- -------- -----
```
Вопрос: Указаны ли в таблице МАС-адресов адреса для VLAN 1? Указаны ли другие МАС-адреса?

Ответ: Нет

Вопрос: Появились ли в таблице МАС-адресов новые адреса спустя 10 секунд?

Ответ: Да

Шаг 4. С компьютера PC-B отправляю эхо-запросы устройствам в сети и смотрю таблицу МАС-адресов коммутатора

1. На компьютере PC-B откройте командную строку и еще раз введите команду arp -a
```
C:\\>arp -a
No ARP Entries Foun
```
Вопрос: Не считая адресов многоадресной и широковещательной рассылки, сколько пар IP- и МАС-адресов устройств было получено через протокол ARP?

Ответ: 0

1. Из командной строки PC-B отправляю эхо-запросы на компьютер PC-A, а также коммутаторы S1 и S2.

Вопрос: От всех ли устройств получены ответы?

Ответ: Да

1. Подключившись через консоль к коммутатору S2, ввожу команду show mac address-table.

Вопрос: Добавил ли коммутатор в таблицу МАС-адресов дополнительные МАС-адреса? Если да, то какие адреса и устройства?

Ответ: Да. Появились MAC-адреса компьютера PC-A, PC-B и S1

1. На компьютере PC-B откройте командную строку и еще раз введите команду arp -a.
```
C:\\>arp -a
Internet Address Physical Address Type
192.168.1.1 0090.0c0d.b430 dynamic
192.168.1.11 000d.bd3a.a9db dynamic
192.168.1.12 0010.1141.08ed dynamic
```
Вопрос: Появились ли в ARP-кэше компьютера PC-B дополнительные записи для всех сетевых устройств, которым были отправлены эхо-запросы?

Ответ: Да

Вопрос для повторения:

В сетях Ethernet данные передаются на устройства по соответствующим МАС-адресам. Для этого коммутаторы и компьютеры динамически создают ARP-кэш и таблицы МАС-адресов. Если компьютеров в сети немного, эта процедура выглядит достаточно простой. Какие сложности могут возникнуть в крупных сетях?

Ответ: В крупных сетях таблица MAC-адресов может переполняться, а ARP-кэш забивать память устройства и перегружать процессор устройства.
