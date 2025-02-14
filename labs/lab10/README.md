Лабораторная работа. Настройка протокола OSPFv2 для одной области

# Топология

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab10/pics/base.png)

# Таблица адресации

| Устройство | Интерфейс | IP-адрес | Маска подсети |
| --- | --- | --- | --- |
| R1  | G0/0/1 | 10.53.0.1 | 255.255.255.0 |
| R1  | Loopback1 | 172.16.1.1 | 255.255.255.0 |
| R2  | G0/0/1 | 10.53.0.2 | 255.255.255.0 |
| R2  | Loopback1 | 192.168.1.1 | 255.255.255.0 |

# Цели

Часть 1. Создание сети и настройка основных параметров устройства

Часть 2. Настройка и проверка базовой работы протокола OSPFv2 для одной области

Часть 3. Оптимизация и проверка конфигурации OSPFv2 для одной области

# Инструкции

## Создание сети и настройка основных параметров устройства

### Создайте сеть согласно топологии

Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

![image](https://github.com/user-attachments/assets/b5bef925-22b3-43ce-8061-bebca0213fd9)


### Произведите базовую настройку маршрутизаторов

Откройте окно конфигурации

- - 1. Назначьте маршрутизатору имя устройства.
- - 2.  Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
- - 3.  Назначьте **class** в качестве зашифрованного пароля привилегированного режима EXEC.
- - 4.  Назначьте **cisco** в качестве пароля консоли и включите вход в систему по паролю.
- - 5.  Назначьте **cisco** в качестве пароля VTY и включите вход в систему по паролю.
- - 6.  Зашифруйте открытые пароли.
- - 7.  Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
- - 8.  Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```
en
conf t
hostname R1
no ip domain-lookup
enable secret class
line con 0
password cisco
login
logging syn
line vty 0 4
password cisco
login
exit
banner motd #UNAUTORIZED ACCESS PROHIBITED!!!#
service pass
exit
clock set 08:58 14 Feb 2025
wr
```

### Настройте базовые параметры каждого коммутатора

- - 1. Назначьте коммутатору имя устройства.
- - 2.  Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
- - 3.  Назначьте **class** в качестве зашифрованного пароля привилегированного режима EXEC.
- - 4.  Назначьте **cisco** в качестве пароля консоли и включите вход в систему по паролю.
- - 5.  Назначьте **cisco** в качестве пароля VTY и включите вход в систему по паролю.
- - 6.  Зашифруйте открытые пароли.
- - 7.  Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
- - 8.  Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```
en
conf t
hostname S1
no ip domain-lookup
enable secret class
line con 0
password cisco
login
logging syn
line vty 0 4
password cisco
login
exit
banner motd #UNAUTORIZED ACCESS PROHIBITED!!!#
service pass
exit
clock set 08:58 14 Feb 2025
wr
```

Закройте окно настройки.

## Настройка и проверка базовой работы протокола OSPFv2 для одной области

### Настройте адреса интерфейса и базового OSPFv2 на каждом маршрутизаторе

- - 1. Настройте адреса интерфейсов на каждом маршрутизаторе, как показано в таблице адресации выше.
```
conf t
int gi 0/0/1
ip addr 10.53.0.1 255.255.255.0
no sh
int loopback1
ip addr 172.16.1.1 255.255.255.0
no sh
do wr
```

Откройте окно конфигурации

- - 1. Перейдите в режим конфигурации маршрутизатора OSPF, используя идентификатор процесса 56.
- - 2.  Настройте статический идентификатор маршрутизатора для каждого маршрутизатора (1.1.1.1 для R1, 2.2.2.2 для R2).
- - 3.  Настройте инструкцию сети для сети между R1 и R2, поместив ее в область 0.
- - 4.  Только на R2 добавьте конфигурацию, необходимую для объявления сети Loopback 1 в область OSPF 0.
- - 5.  Убедитесь, что OSPFv2 работает между маршрутизаторами. Выполните команду, чтобы убедиться, что R1 и R2 сформировали смежность.
R1
```
conf t
router ospf 56
router-id 1.1.1.1
network 10.53.0.0 0.0.0.255 area 0
exit
do wr
```
R2
```
conf t
router ospf 56
router-id 2.2.2.2
network 10.53.0.0 0.0.0.255 area 0
network 192.168.1.0 0.0.0.255 area 0
exit
do wr
```
```
R1#sh ip route 
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

     10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C       10.53.0.0/24 is directly connected, GigabitEthernet0/0/1
L       10.53.0.1/32 is directly connected, GigabitEthernet0/0/1
     172.16.0.0/16 is variably subnetted, 2 subnets, 2 masks
C       172.16.1.0/24 is directly connected, Loopback1
L       172.16.1.1/32 is directly connected, Loopback1
     192.168.1.0/32 is subnetted, 1 subnets
O       192.168.1.1/32 [110/2] via 10.53.0.2, 00:05:09, GigabitEthernet0/0/1

```
#### Вопрос
- - 1. Какой маршрутизатор является DR? Какой маршрутизатор является BDR? Каковы критерии отбора?
    Ответ: DR является маршрутизатор R2. BDR маршрутизатор R1. При одинаковых приоритетах интрефейсов критерием отбора становится наивысший router-id.
- - 2.  На R1 выполните команду **show ip route ospf**, чтобы убедиться, что сеть R2 Loopback1 присутствует в таблице маршрутизации. Обратите внимание, что поведение OSPF по умолчанию заключается в объявлении интерфейса обратной связи в качестве маршрута узла с использованием 32-битной маски.
```
R1#sh ip route ospf
     192.168.1.0/32 is subnetted, 1 subnets
O       192.168.1.1 [110/2] via 10.53.0.2, 00:06:17, GigabitEthernet0/0/1
```
- - 4.  Запустите Ping до адреса интерфейса R2 Loopback 1 из R1. Выполнение команды ping должно быть успешным.
```
R1#ping 192.168.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/2/13 ms
```

Закройте окно настройки.

## Оптимизация и проверка конфигурации OSPFv2 для одной области

### Реализация различных оптимизаций на каждом маршрутизаторе

Откройте окно конфигурации

- - 1. На R1 настройте приоритет OSPF интерфейса G0/0/1 на 50, чтобы убедиться, что R1 является назначенным маршрутизатором.
- - 2.  Настройте таймеры OSPF на G0/0/1 каждого маршрутизатора для таймера приветствия, составляющего 30 секунд.
- - 3.  На R1 настройте статический маршрут по умолчанию, который использует интерфейс Loopback 1 в качестве интерфейса выхода. Затем распространите маршрут по умолчанию в OSPF. Обратите внимание на сообщение консоли после установки маршрута по умолчанию.
```
%Default route without gateway, if not a point-to-point interface, may impact performance
```
- - 4.  добавьте конфигурацию, необходимую для OSPF для обработки R2 Loopback 1 как сети точка-точка. Это приводит к тому, что OSPF объявляет Loopback 1 использует маску подсети интерфейса.
- - 5.  Только на R2 добавьте конфигурацию, необходимую для предотвращения отправки объявлений OSPF в сеть Loopback 1.
- - 6.  Измените базовую пропускную способность для маршрутизаторов. После этой настройки перезапустите OSPF с помощью команды **clear ip ospf process** . Обратите внимание на сообщение консоли после установки новой опорной полосы пропускания.
R1
```
conf t
int gi 0/0/1
ip ospf priority 50
ip ospf hello-interval 30
ip ospf dead-interval 120
exit
ip route 0.0.0.0 0.0.0.0 loopback 1
router ospf 56
default-information originate
ex
router ospf 56
auto-cost reference-bandwidth 1000
end
clear ip ospf process
Reset ALL OSPF processes? [no]: y
wr
```
R2
```
conf t
int gi 0/0/1
ip ospf hello-interval 30
ip ospf dead-interval 120
int loop 1
ip ospf network point-to-point
ex
router ospf 56
passive-interface loop 1
auto-cost reference-bandwidth 1000
end
clear ip ospf process
Reset ALL OSPF processes? [no]: y
```

### Убедитесь, что оптимизация OSPFv2 реализовалась

- - 1. Выполните команду **show ip ospf interface g0/0/1** на R1 и убедитесь, что приоритет интерфейса установлен равным 50, а временные интервалы — Hello 30, Dead 120, а тип сети по умолчанию — Broadcast
```
R1#show ip ospf interface g0/0/1

GigabitEthernet0/0/1 is up, line protocol is up
  Internet address is 10.53.0.1/24, Area 0
  Process ID 56, Router ID 1.1.1.1, **Network Type BROADCAST**, Cost: 1
  Transmit Delay is 1 sec, State DR, **Priority 50**
  Designated Router (ID) 1.1.1.1, Interface address 10.53.0.1
  No backup designated router on this network
  Timer intervals configured, **Hello 30, Dead 120**, Wait 120, Retransmit 5
    Hello due in 00:00:17
  Index 1/1, flood queue length 0
  Next 0x0(0)/0x0(0)
  Last flood scan length is 1, maximum is 1
  Last flood scan time is 0 msec, maximum is 0 msec
  Neighbor Count is 1, Adjacent neighbor count is 1
    Adjacent with neighbor 2.2.2.2
  Suppress hello for 0 neighbor(s)
```
- - 2.  На R1 выполните команду **show ip route ospf**, чтобы убедиться, что сеть R2 Loopback1 присутствует в таблице маршрутизации. Обратите внимание на разницу в метрике между этим выходным и предыдущим выходным. Также обратите внимание, что маска теперь составляет 24 бита, в отличие от 32 битов, ранее объявленных.
```
R1#show ip ospf interface g0/0/1

GigabitEthernet0/0/1 is up, line protocol is up
  Internet address is 10.53.0.1/24, Area 0
  Process ID 56, Router ID 1.1.1.1, Network Type BROADCAST, Cost: 1
  Transmit Delay is 1 sec, State DR, Priority 50
  Designated Router (ID) 1.1.1.1, Interface address 10.53.0.1
  No backup designated router on this network
  Timer intervals configured, Hello 30, Dead 120, Wait 120, Retransmit 5
    Hello due in 00:00:17
  Index 1/1, flood queue length 0
  Next 0x0(0)/0x0(0)
  Last flood scan length is 1, maximum is 1
  Last flood scan time is 0 msec, maximum is 0 msec
  Neighbor Count is 1, Adjacent neighbor count is 1
    Adjacent with neighbor 2.2.2.2
  Suppress hello for 0 neighbor(s)
R1#sh ip route ospf
O    192.168.1.0 [110/1] via 10.53.0.2, 00:02:40, GigabitEthernet0/0/1
```
- - 3.  Введите команду **show ip route ospf** на маршрутизаторе R2. Единственная информация о маршруте OSPF должна быть распространяемый по умолчанию маршрут R1.
```
R2#sh ip route ospf
O*E2 0.0.0.0/0 [110/1] via 10.53.0.1, 00:04:09, GigabitEthernet0/0/1
```
- - 4.  Запустите Ping до адреса интерфейса R1 Loopback 1 из R2. Выполнение команды ping должно быть успешным.
```
R2#ping 172.16.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 172.16.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/1 ms
```

#### Вопрос

Почему стоимость OSPF для маршрута по умолчанию отличается от стоимости OSPF в R1 для сети 192.168.1.0/24?
**Ответ:** Потому что для интерфейса loopback, который указан в маршруте по умолчанию, стоимость маршрута всегда является единицей (cost = 1) а для остальных интерфейсоф ospf рассчитывает стоимость по формуле cost = Reference Bandwidth / Interface Bandwidth
