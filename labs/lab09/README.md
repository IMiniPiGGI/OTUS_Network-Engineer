# Лабораторная работа - Конфигурация безопасности коммутатора

## Топология

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab09/pics/pic_base.png)

## Таблица адресации

| Устройство | interface/vlan | IP-адрес | Маска подсети |
| --- | --- | --- | --- |
| R1  | G0/0/1 | 192.168.10.1 | 255.255.255.0 |
| R1  | Loopback 0 | 10.10.1.1 | 255.255.255.0 |
| S1  | VLAN 10 | 192.168.10.201 | 255.255.255.0 |
| S2  | VLAN 10 | 192.168.10.202 | 255.255.255.0 |
| PC A | NIC | DHCP | 255.255.255.0 |
| PC B | NIC | DHCP | 255.255.255.0 |

## Цели

### Часть 1. Настройка основного сетевого устройства

1. Создайте сеть.
2. Настройте маршрутизатор R1.
3. Настройка и проверка основных параметров коммутатора

### Часть 2. Настройка сетей VLAN

1. Сконфигруриуйте VLAN 10.
2. Сконфигруриуйте SVI для VLAN 10.
3. Настройте VLAN 333 с именем Native на S1 и S2.
4. Настройте VLAN 999 с именем ParkingLot на S1 и S2.

### Часть 3: Настройки безопасности коммутатора

1. Реализация магистральных соединений 802.1Q.
2. Настройка портов доступа
3. Безопасность неиспользуемых портов коммутатора
4. Документирование и реализация функций безопасности порта.
5. Реализовать безопасность DHCP snooping .
6. Реализация PortFast и BPDU Guard
7. Проверка сквозной связанности.

# Решение

## Часть 1. Настройка основного сетевого устройства

### Шаг 1. Создайте сеть

1. Создайте сеть согласно топологии.
2. Инициализация устройств

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab09/pics/pic_1.png)

### Шаг 2. Настройте маршрутизатор R1

1. Загрузите следующий конфигурационный скрипт на R1.
```
enable
configure terminal
hostname R1
no ip domain lookup
ip dhcp excluded-address 192.168.10.1 192.168.10.9
ip dhcp excluded-address 192.168.10.201 192.168.10.202
!
ip dhcp pool Students
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
domain-name CCNA2.Lab-11.6.1
ip dhcp relay information trust-all – воспользовался этой командой вместо relay на порту G0/0/1
!
interface Loopback0
ip address 10.10.1.1 255.255.255.0
!
interface GigabitEthernet0/0/1
description Link to S1
ip dhcp relay information trusted – В CPT не получилось воспользоваться этой командой
ip address 192.168.10.1 255.255.255.0
no shutdown
!
line con 0
logging synchronous
exec-timeout 0 0
```
2. Проверьте текущую конфигурацию на R1, используя следующую команду:
```
R1# show ip interface brief
R1(config-line)#do sh ip int brief
Interface IP-Address OK? Method Status Protocol
GigabitEthernet0/0/0 unassigned YES unset administratively down down
GigabitEthernet0/0/1 192.168.10.1 YES manual up up
Loopback0 10.10.1.1 YES manual up up
Vlan1 unassigned YES unset administratively down down
```
3. Убедитесь, что IP-адресация и интерфейсы находятся в состоянии up / up (при необходимости устраните неполадки).

### Шаг 3. Настройка и проверка основных параметров коммутатора

1. Настройте имя хоста для коммутаторов S1 и S2.
2. Запретите нежелательный поиск в DNS.
3. Настройте описания интерфейса для портов, которые используются в S1 и S2.
4. Установите для шлюза по умолчанию для VLAN управления значение 192.168.10.1 на обоих коммутаторах.

### S1
```
Switch>en
Switch#conf t
Switch(config)#hostname S1
S1(config)#ip domain-lookup
S1(config)#int fa 0/5
S1(config-if)#description To-R1
S1(config-if)#int fa 0/6
S1(config-if)#description To-PC-A
S1(config-if)#int fa 0/1
S1(config-if)#description To-S2
S1(config-if)#exit
S1(config)#ip default-gateway 192.168.10.1
```
### S2
```
Switch>en
Switch#conf t
Switch(config)#hostname S2
S2(config)#int fa 0/1
S2(config-if)#desc To-S1
S2(config-if)#int fa 0/18
S2(config-if)#desc To-PC-B
S2(config-if)#ex
S2(config)#ip domain-lookup
S2(config)#ip default-gateway 192.168.10.1
```
## Часть 2. Настройка сетей VLAN на коммутаторах

### Шаг 1. Сконфигурируйте VLAN 10

Добавьте VLAN 10 на S1 и S2 и назовите VLAN - Management.
```
S1(config)#vlan 10
S1(config-vlan)#name Management
```
### Шаг 2. Сконфигруриуйте SVI для VLAN 10

Настройте IP-адрес в соответствии с таблицей адресации для SVI для VLAN 10 на S1 и S2. Включите интерфейсы SVI и предоставьте описание для интерфейса.
```
S1(config-vlan)#int vlan 10
S1(config-if)#ip addr 192.168.10.201 255.255.255.0
S1(config-if)#no sh
S1(config-if)#description Mangement-SVI
```
### Шаг 3. Настройте VLAN 333 с именем Native на S1 и S2
```
S1(config)#vlan 333
S1(config-vlan)#name Native
```
### Шаг 4. Настройте VLAN 999 с именем ParkingLot на S1 и S2
```
S1(config-vlan)#vlan 999
S1(config-vlan)#name ParkingLot
```
## Часть 3. Настройки безопасности коммутатора

### Шаг 1. Релизация магистральных соединений 802.1Q

1. Настройте все магистральные порты Fa0/1 на обоих коммутаторах для использования VLAN 333 в качестве native VLAN.
```
S1(config-vlan)#int fa 0/1
S1(config-if)#sw m trunk
S1(config-if)#sw tr native vlan 333
```
2. Убедитесь, что режим транкинга успешно настроен на всех коммутаторах.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab09/pics/pic_2.png)

3. Отключить согласование DTP F0/1 на S1 и S2.
```
S1(config-if)#switchport nonegotiate
```
4. Проверьте с помощью команды show interfaces.
```
S1(config-if)#do sh int fa0/1 sw | i Negotiation
Negotiation of Trunking: Off
```
### Шаг 2. Настройка портов доступа

1. На S1 настройте F0/5 и F0/6 в качестве портов доступа и свяжите их с VLAN 10.
```
S1(config-if)#int range fa0/5-6
S1(config-if-range)#sw m ac
S1(config-if-range)#sw ac vl 10
```
2. На S2 настройте порт доступа Fa0/18 и свяжите его с VLAN 10.
```
S2(config-if)#int fa 0/18
S2(config-if)#sw m ac
S2(config-if)#sw ac vl 10
```
### Шаг 3. Безопасность неиспользуемых портов коммутатора

1. На S1 и S2 переместите неиспользуемые порты из VLAN 1 в VLAN 999 и отключите неиспользуемые порты.
```
S1(config)#int range fa0/2-4,fa0/7-24,gi0/1-2
S1(config-if-range)#sw m ac
S1(config-if-range)#sw ac vl 999
S1(config-if-range)#sh
```
2. Убедитесь, что неиспользуемые порты отключены и связаны с VLAN 999, введя команду show.

### S1

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab09/pics/pic_3.png)

### S2

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab09/pics/pic_4.png)

### Шаг 4. Документирование и реализация функций безопасности порта

1. Интерфейсы F0/6 на S1 и F0/18 на S2 настроены как порты доступа. На этом шаге вы также настроите безопасность портов на этих двух портах доступа.
2. На S1, введите команду show port-security interface f0/6 для отображения настроек по умолчанию безопасности порта для интерфейса F0/6. Запишите свои ответы ниже.

| Конфигурация безопасности порта по умолчанию |     |
| --- |     | --- |
| Функция | Настройка по умолчанию |
| --- | --- |
| Защита портов | Disabled |
| Максимальное количество записей MAC-адресов | 1   |
| Режим проверки на нарушение безопасности | Shutdown |
| Aging Time | 0 mins |
| Aging Type | Absolute |
| Secure Static Address Aging | Disabled |
| Sticky MAC Address | 0   |

3. На S1 включите защиту порта на F0 / 6 со следующими настройками:
    1. Максимальное количество записей MAC-адресов: 3
```
S1(config-if)#sw port-sec
S1(config-if)#switchport port-security maximum 3
```
   2 Режим безопасности: restrict
```
S1(config-if)#sw port-sec violation restrict
```
   3. Aging time: 60 мин.
```
S1(config-if)#sw port-sec aging time 60
```
   4. Aging type: неактивный
```
S1(config-if)#switchport port-security aging type inactive
```
К сожалению, в CPT нет возможности установить тип aging

4. Verify port security on S1 F0/6.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab09/pics/pic_5.png)

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab09/pics/pic_6.png)

5. Включите безопасность порта для F0 / 18 на S2. Настройте каждый активный порт доступа таким образом, чтобы он автоматически добавлял адреса МАС, изученные на этом порту, в текущую конфигурацию.
```
S2(config)#int fa 0/18
S2(config-if)#sw port-security
S2(config-if)#sw port-security mac-address sticky
```
6. Настройте следующие параметры безопасности порта на S2 F / 18:
    1. Максимальное количество записей MAC-адресов: 2
    2. Тип безопасности: Protect
    3. Aging time: 60 мин.
```
S2(config-if)#sw port-sec maximum 2
S2(config-if)#sw port-sec viol protect
S2(config-if)#sw port-sec aging time 60
```
7. Проверка функции безопасности портов на S2 F0/18.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab09/pics/pic_7.png)

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab09/pics/pic_8.png)

### Шаг 5. Реализовать безопасность DHCP snooping

1. На S2 включите DHCP snooping и настройте DHCP snooping во VLAN 10.
2. Настройте магистральные порты на S2 как доверенные порты.
3. Ограничьте ненадежный порт Fa0/18 на S2 пятью DHCP-пакетами в секунду.
4. Проверка DHCP Snooping на S2.
```
S2(config)#ip dhcp snooping vlan 10
S2(config)#int fa 0/1
S2(config-if)#ip dhcp snooping trust
S2(config-if)#int fa 0/18
S2(config-if)#ip dhcp snooping limit rate 5
```
![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab09/pics/pic_9.png)

5. В командной строке на PC-B освободите, а затем обновите IP-адрес.
```
C:\Users\Student> ipconfig /release
C:\Users\Student> ipconfig /renew
```
![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab09/pics/pic_10.png)

6. Проверьте привязку отслеживания DHCP с помощью команды show ip dhcp snooping binding.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab09/pics/pic_11.png)

### Шаг 6. Реализация PortFast и BPDU Guard

1. Настройте PortFast на всех портах доступа, которые используются на обоих коммутаторах.
```
S1#conf t
Enter configuration commands, one per line. End with CNTL/Z.
S1(config)#int ra fa0/5-6
S1(config-if-range)#spanning-tree portfast
```
2. Включите защиту BPDU на портах доступа VLAN 10 S1 и S2, подключенных к PC-A и PC-B.
```
S1(config-if)#spanning-tree bpduguard enable
```
3. Убедитесь, что защита BPDU и PortFast включены на соответствующих портах.
```
S1# show spanning-tree interface f0/6 detail
```
У меня не получилось сделать вывод, как в задании, при выполнении show spanning-tree interface f0/6 detail нет информации о том, что BPDU активен и счётчика пакетов, однако проверяя работоспособность на S2 подключил к Fa0/18 коммутатор, чтобы сгенерировать BPDU пакеты, после чего порт упал с ошибкой о нарушении BPDU. Поэтому могу сделать вывод, что защита BPDU включена на портах.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab09/pics/pic_12.png)

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab09/pics/pic_13.png)

При этом выполнение команды после перезагрузки порта даёт вот такую картину:

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab09/pics/pic_14.png)

А во время, пока порт заблокирован, там, естественно, совершенно пусто.

### Шаг 7. Проверьте наличие сквозного ⁪подключения

Проверьте PING свзяь между всеми устройствами в таблице IP-адресации. В случае сбоя проверки связи может потребоваться отключить брандмауэр на хостах.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab09/pics/pic_15.png)

# Вопросы для повторения

1. С точки зрения безопасности порта на S2, почему нет значения таймера для оставшегося возраста в минутах, когда было сконфигурировано динамическое обучение - sticky?

Ответ: В случае sticky обучения mac-адреса записываются в конфигурацию и сохраняются даже после перезагрузки, поэтому таймер устаревания на них не применяется.

2. Что касается безопасности порта на S2, если вы загружаете скрипт текущей конфигурации на S2, почему порту 18 на PC-B никогда не получит IP-адрес через DHCP?

Ответ: Потому что на порту настроен Sticky доступ

3. Что касается безопасности порта, в чем разница между типом абсолютного устаревания и типом устаревание по неактивности?

Ответ: Абсолютное устаревание, это режим, при котором MAC-адреса будут удалены из таблицы адресов коммутатора после установленного времени, вне зависимости от того, используются ли они или нет, устаревание по неактивности, это режим, при котором MAC-адреса будут удалены из таблицы адресов только если не осуществлялось активности с их стороны в течение заранее установленного времени
