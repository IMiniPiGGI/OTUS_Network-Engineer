# Лабораторная работа. Настройка IPv6-адресов на сетевых устройствах

# Топология

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab04/pics/pic%20base.png)

# Таблица адресации

<table><tbody><tr><th><p>Устройство</p></th><th><p>Интерфейс</p></th><th><p>IPv6-адрес</p></th><th><p>Link local IPv6-адрес</p></th><th><p>Длина префикса</p></th><th><p>Шлюз по умолчанию</p></th></tr><tr><td><p>R1</p></td><td><p>G0/0/0</p></td><td><p>2001:db8:acad:a::1</p></td><td><p>fe80::1</p></td><td><p>64</p></td><td><p>—</p></td></tr><tr><td>R1</td><td><p>G0/0/1</p></td><td><p>2001:db8:acad:1::1</p></td><td><p>fe80::1</p></td><td><p>64</p></td><td><p>—</p></td></tr><tr><td><p>S1</p></td><td><p>VLAN 1</p></td><td><p>2001:db8:acad:1::b</p></td><td><p>fe80::b</p></td><td><p>64</p></td><td><p>—</p></td></tr><tr><td><p>PC-A</p></td><td><p>NIC</p></td><td><p>2001:db8:acad:1::3</p></td><td><p>SLACC</p></td><td><p>64</p></td><td><p>fe80::1</p></td></tr><tr><td><p>PC-B</p></td><td><p>NIC</p></td><td><p>2001:db8:acad:a::3</p></td><td><p>SLACC</p></td><td><p>64</p></td><td><p>fe80::1</p></td></tr></tbody></table>

# Задачи

Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора

Часть 2. Ручная настройка IPv6-адресов

Часть 3. Проверка сквозного соединения

# Решение

## Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора
Подключаю оборудование согласно топологии:

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab04/pics/pic%201.png)

После подключения сети, инициализации и перезагрузки маршрутизатора и коммутатора выполняю следующие действия:

### Шаг 1. Настраиваю маршрутизатор.

Назначаю имя хоста и настраиваю основные параметры устройства.
```
enable
conf t
service password-encryption
hostname R1
enable secret class
no ip domain-lookup
line con 0
password cisco
logging synchronous
line vty 0 4
password cisco login
```
### Шаг 2. Настраиваю коммутатор.

Назначаю имя хоста и настраиваю основные параметры устройства.
```
enable
conf t
sdm prefer dual-ipv4-and-ipv6 default
service password-encryption
hostname S1
enable secret class
no ip domain-lookup
line con 0
password cisco
logging synchronous
line vty 0 4
password cisco login
do wr
reload
```
## Часть 2. Ручная настройка IPv6-адресов

### Шаг 1. Назначаю IPv6-адреса интерфейсам Ethernet на R1.

1. Назначаю глобальные индивидуальные IPv6-адреса, указанные в таблице адресации обоим интерфейсам Ethernet на R1.
```
conf t
int gi 0/0/0
ipv6 address 2001:db8:acad:a::1/64
no sh
int gi 0/0/1
ipv6 address 2001:db8:acad:1::1/64
no sh
```
1. Ввожу команду show ipv6 interface brief, чтобы проверить, назначен ли каждому интерфейсу корректный индивидуальный IPv6-адрес.
```
R1#show ipv6 interface brief
GigabitEthernet0/0/0 [up/up]
FE80::202:17FF:FE9C:3101
2001:DB8:ACAD:A::1
GigabitEthernet0/0/1 [up/up]
FE80::202:17FF:FE9C:3102
2001:DB8:ACAD:1::1
vlan1 [administratively down/down]
unassigned
```
1. Чтобы обеспечить соответствие локальных адресов канала индивидуальному адресу, вручную ввожу локальные адреса канала на каждом интерфейсе Ethernet на R1.
```
int gi 0/0/0
ipv6 address fe80::1 link-local
int gi 0/0/1
ipv6 address fe80::1 link-local
```
1. Используя выбранную команду, убеждаюсь, что локальный адрес связи изменен на fe80::1.
```
R1#sh ipv6 interface brief
GigabitEthernet0/0/0 [up/up]
FE80::1
2001:DB8:ACAD:A::1
GigabitEthernet0/0/1 [up/up]
FE80::1
2001:DB8:ACAD:1::1
vlan1 [administratively down/down]
unassigned
```
Вопрос: Какие группы многоадресной рассылки назначены интерфейсу G0/0?

Ответ: Глобальная группа (2001:DB8:ACAD:\*::1) и группа локального канала связи (FE80::1)

### Шаг 2. Активирую IPv6-маршрутизацию на R1.

1. В командной строке на PC-B ввожу команду ipconfig, чтобы получить данные IPv6-адреса, назначенного интерфейсу ПК.
```
C:\>ipconfig

FastEthernet0 Connection:(default port)
Connection-specific DNS Suffix..:
Link-local IPv6 Address.........: FE80::2D0:58FF:FE39:60A3
IPv6 Address....................: ::
IPv4 Address....................: 0.0.0.0
Subnet Mask.....................: 0.0.0.0
Default Gateway.................: ::
0.0.0.0
```
Вопрос: Назначен ли индивидуальный IPv6-адрес сетевой интерфейсной карте (NIC) на PC-B?

Ответ: Да

1. Активирую IPv6-маршрутизацию на R1 с помощью команды IPv6 unicast-routing.
2. Теперь, когда R1 входит в группу многоадресной рассылки всех маршрутизаторов, еще раз ввожу команду ipconfig на PC-B. Провяю данные IPv6-адреса.
```
C:\>ipconfig

FastEthernet0 Connection:(default port)
Connection-specific DNS Suffix..:
Link-local IPv6 Address.........: FE80::2D0:58FF:FE39:60A3
IPv6 Address....................: 2001:DB8:ACAD:1:2D0:58FF:FE39:60A3
IPv4 Address....................: 0.0.0.0
Subnet Mask.....................: 0.0.0.0
Default Gateway.................: FE80::1
0.0.0.0
```
Вопрос: Почему PC-B получил глобальный префикс маршрутизации и идентификатор подсети, которые вы настроили на R1?

Ответ: Роутер, настроенный на IPv6, отправляет объявления (RA), в которых указывается, какой глобальный префикс (основная часть адреса) он использует, PC-B берет этот глобальный префикс и добавляет к нему свою уникальную часть (идентификатор), чтобы сформировать полный IPv6-адрес.

### Шаг 3. Назначаю IPv6-адреса интерфейсу управления (SVI) на S1.

1. Назначаю адрес IPv6 для S1. Также назначаю этому интерфейсу локальный адрес канала fe80::b.
2. Проверяю правильность назначения IPv6-адресов интерфейсу управления с помощью команды show ipv6 interface vlan1.
```
S1#sh ipv6 interface vlan 1
Vlan1 is up, line protocol is up
IPv6 is enabled, link-local address is FE80::B
No Virtual link-local address(es):
Global unicast address(es):
2001:DB8:ACAD:1::B, subnet is 2001:DB8:ACAD:1::/64
Joined group address(es):
FF02::1
FF02::1:FF00:B
MTU is 1500 bytes
ICMP error messages limited to one every 100 milliseconds
ICMP redirects are enabled
ICMP unreachables are sent
Output features: Check hwidb
ND DAD is enabled, number of DAD attempts: 1
ND reachable time is 30000 milliseconds
```
### Шаг 4. Назначьте компьютерам статические IPv6-адреса.
1. Откройте окно Свойства Ethernet для каждого ПК и назначьте адресацию IPv6.
2. Убедитесь, что оба компьютера имеют правильную информацию адреса IPv6. Каждый компьютер должен иметь два глобальных адреса IPv6: один статический и один SLACC.

SLAACC:

PC-A

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab04/pics/pic%202.png)

PC-B

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab04/pics/pic%203.png)

Static:

PC-A

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab04/pics/pic%204.png)

PC-B

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab04/pics/pic%205.png)

## Часть 3. Проверка сквозного подключения

С PC-A отправляю эхо-запрос на FE80::1. Это локальный адрес канала, назначенный G0/1 на R1.
```
C:\>ping fe80::1

Pinging fe80::1 with 32 bytes of data:

Reply from FE80::1: bytes=32 time<1ms TTL=255
Reply from FE80::1: bytes=32 time<1ms TTL=255
Reply from FE80::1: bytes=32 time<1ms TTL=255
Reply from FE80::1: bytes=32 time<1ms TTL=255

Ping statistics for FE80::1:

Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
Minimum = 0ms, Maximum = 0ms, Average = 0ms
```
Отправляю эхо-запрос на интерфейс управления S1 с PC-A.
```
C:\>ping 2001:db8:acad:1::b

Pinging 2001:db8:acad:1::b with 32 bytes of data:

Reply from 2001:DB8:ACAD:1::B: bytes=32 time<1ms TTL=255
Reply from 2001:DB8:ACAD:1::B: bytes=32 time<1ms TTL=255
Reply from 2001:DB8:ACAD:1::B: bytes=32 time<1ms TTL=255
Reply from 2001:DB8:ACAD:1::B: bytes=32 time<1ms TTL=255

Ping statistics for 2001:DB8:ACAD:1::B:

Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
Minimum = 0ms, Maximum = 0ms, Average = 0ms
```
Ввожу команду tracert на PC-A, чтобы проверить наличие сквозного подключения к PC-B.
```
C:\>tracert 2001:db8:acad:a::3

Tracing route to 2001:db8:acad:a::3 over a maximum of 30 hops:

1 0 ms 0 ms 0 ms 2001:DB8:ACAD:1::1
2 0 ms 0 ms 0 ms 2001:DB8:ACAD:A::3

Trace complete.
```
С PC-B отправляю эхо-запрос на PC-A.
```
C:\>ping 2001:DB8:ACAD:1::3

Pinging 2001:DB8:ACAD:1::3 with 32 bytes of data:

Reply from 2001:DB8:ACAD:1::3: bytes=32 time<1ms TTL=127
Reply from 2001:DB8:ACAD:1::3: bytes=32 time<1ms TTL=127
Reply from 2001:DB8:ACAD:1::3: bytes=32 time<1ms TTL=127
Reply from 2001:DB8:ACAD:1::3: bytes=32 time<1ms TTL=127

Ping statistics for 2001:DB8:ACAD:1::3:

Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
Minimum = 0ms, Maximum = 0ms, Average = 0ms
```
С PC-B отправляю эхо-запрос на локальный адрес канала G0/0 на R1.
```
C:\>ping FE80::1

Pinging FE80::1 with 32 bytes of data:

Reply from FE80::1: bytes=32 time<1ms TTL=255
Reply from FE80::1: bytes=32 time<1ms TTL=255
Reply from FE80::1: bytes=32 time<1ms TTL=255
Reply from FE80::1: bytes=32 time=1ms TTL=255

Ping statistics for FE80::1:

Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
Minimum = 0ms, Maximum = 1ms, Average = 0ms
```
# Вопросы для повторения

Вопрос: Почему обоим интерфейсам Ethernet на R1 можно назначить один и тот же локальный адрес канала — FE80::1?

Ответ: Потому что локальные адреса используются для связи между устройствами в пределах одной подсети и не маршрутизируются за ее пределами, а каждый интерфейс маршрутизатора – это отдельная подсеть

Вопрос: Какой идентификатор подсети в индивидуальном IPv6-адресе 2001:db8:acad::aaaa:1234/64?

Ответ: 2001:db8:acad:0000
