Лабораторная работа. Настройка и проверка расширенных списков контроля доступа.

# Топология

![image](https://github.com/user-attachments/assets/e422e10b-0a59-4fd7-9fc1-5016044c591a)

# Таблица адресации

| Устройство | Интерфейс | IP-адрес | Маска подсети | Шлюз по умолчанию |
| --- | --- | --- | --- | --- |
| R1  | G0/0/1 | —   | —   | —   |
| R1  | G0/0/1.20 | 10.20.0.1 | 255.255.255.0 | —   |
| R1  | G0/0/1.30 | 10.30.0.1 | 255.255.255.0 | —   |
| R1  | G0/0/1.40 | 10.40.0.1 | 255.255.255.0 | —   |
| R1  | G0/0/1.1000 | —   | —   | —   |
| R1  | Loopback1 | 172.16.1.1 | 255.255.255.0 | —   |
| R2  | G0/0/1 | 10.20.0.4 | 255.255.255.0 | —   |
| S1  | VLAN 20 | 10.20.0.2 | 255.255.255.0 | 10.20.0.1 |
| S2  | VLAN 20 | 10.20.0.3 | 255.255.255.0 | 10.20.0.1 |
| PC-A | NIC | 10.30.0.10 | 255.255.255.0 | 10.30.0.1 |
| PC-B | NIC | 10.40.0.10 | 255.255.255.0 | 10.40.0.1 |

# Таблица VLAN

| VLAN | Имя | Назначенный интерфейс |
| --- | --- | --- |
| 20  | Management | S2: F0/5 |
| 30  | Operations | S1: F0/6 |
| 40  | Sales | S2: F0/18 |
| 999 | ParkingLot | S1: F0/2-4, F0/7-24, G0/1-2<br><br>S2: F0/2-4, F0/6-17, F0/19-24, G0/1-2 |
| 1000 | Собственная | —   |

# Задачи

Часть 1. Создание сети и настройка основных параметров устройства

Часть 2. Настройка и проверка списков расширенного контроля доступа

# Инструкции

## Создание сети и настройка основных параметров устройства

### Создайте сеть согласно топологии

Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.
![image](https://github.com/user-attachments/assets/24e37345-d2ab-474f-bde4-b19534debeac)


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
clock set 13:01 14 Feb 2025
wr
```

Закройте окно настройки.

### Настройте базовые параметры каждого коммутатора

Откройте окно конфигурации

- - 1. Присвойте коммутатору имя устройства.
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
clock set 13:05 14 Feb 2025
wr
```

Закройте окно настройки.

## Настройка сетей VLAN на коммутаторах

### Создайте сети VLAN на коммутаторах

Откройте окно конфигурации

- - 1. Создайте необходимые VLAN и назовите их на каждом коммутаторе из приведенной выше таблицы.
- - 2.  Настройте интерфейс управления и шлюз по умолчанию на каждом коммутаторе, используя информацию об IP-адресе в таблице адресации.
- - 3.  Назначьте все неиспользуемые порты коммутатора VLAN Parking Lot, настройте их для статического режима доступа и административно деактивируйте их.

**Примечание.** Команда interface range полезна для выполнения этой задачи с помощью необходимого количества команд.

S1
```
conf t
vl 30
name Operations
ex
vl 40
name Sales
ex
vlan 999
name ParkingLot
ex
vlan 20
name Management
ex
int vlan 20
ip addr 10.20.0.2 255.255.255.0
no sh
ex
ip default-gateway 10.20.0.1
int ra fa0/2-4, fa0/7-24, gi0/1-2
sw m ac
sw ac vl 999
sh
end
wr
```
S2
```
conf t
vlan 40
name Sales
ex
vlan 999
name ParkingLot
ex
vlan 20
name Management
ex
vlan 30
name Operations
ex
int vlan 20
ip addr 10.20.0.3 255.255.255.0
no sh
ex
ip default-gateway 10.20.0.1
int ra fa0/2-4, fa0/6-17, fa0/19-24, gi0/1-2
sw m ac
sw ac vl 999
sh
end
wr
```

### Назначьте сети VLAN соответствующим интерфейсам коммутатора

- - 1. Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.
- - 2.  Выполните команду **show vlan brief**, чтобы убедиться, что сети VLAN назначены правильным интерфейсам.
    
S1
```
conf t
int fa0/6
sw m ac
sw ac vl 30
end
wr
```
```
S1#sh vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    
20   Management                       active    
30   Operations                       active    Fa0/6
40   Sales                            active    
999  ParkingLot                       active    Fa0/2, Fa0/3, Fa0/4, Fa0/7
                                                Fa0/8, Fa0/9, Fa0/10, Fa0/11
                                                Fa0/12, Fa0/13, Fa0/14, Fa0/15
                                                Fa0/16, Fa0/17, Fa0/18, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24, Gig0/1, Gig0/2
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    
```
S2
```
conf t
int fa0/5
sw m ac
sw ac vl 20
ex
int fa0/18
sw m ac
sw ac vl 40
end
wr
```
```
S2#sh vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    
20   Management                       active    Fa0/5
30   Operations                       active    
40   Sales                            active    Fa0/18
999  ParkingLot                       active    Fa0/2, Fa0/3, Fa0/4, Fa0/6
                                                Fa0/7, Fa0/8, Fa0/9, Fa0/10
                                                Fa0/11, Fa0/12, Fa0/13, Fa0/14
                                                Fa0/15, Fa0/16, Fa0/17, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24, Gig0/1, Gig0/2
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    
```

Закройте окно настройки.

## ·Настройте транки (магистральные каналы)

### Вручную настройте магистральный интерфейс F0/1

Откройте окно конфигурации

- - 1. Измените режим порта коммутатора на интерфейсе F0/1, чтобы принудительно создать магистральную связь. Не забудьте сделать это на обоих коммутаторах.
- - 2.  В рамках конфигурации транка установите для native vlan значение 1000 на обоих коммутаторах. При настройке двух интерфейсов для разных собственных VLAN сообщения об ошибках могут отображаться временно.
- - 3.  В качестве другой части конфигурации транка укажите, что VLAN 10, 20, 30 и 1000 разрешены в транке.
- - 4.  Выполните команду **show interfaces trunk** для проверки портов магистрали, собственной VLAN и разрешенных VLAN через магистраль.
```
conf t
int fa 0/1
sw m trunk
sw tr native vl 1000
sw tr allowed vl 10,20,30,1000
end
wr
```
```
S1#sh int trunk
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      1000

Port        Vlans allowed on trunk
Fa0/1       10,20,30,1000

Port        Vlans allowed and active in management domain
Fa0/1       20,30

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       20,30
```
### Вручную настройте магистральный интерфейс F0/5 на коммутаторе S1

- - 1. Настройте интерфейс S1 F0/5 с теми же параметрами транка, что и F0/1. Это транк до маршрутизатора.
- - 2.  Сохраните текущую конфигурацию в файл загрузочной конфигурации.
- - 3.  Используйте команду **show interfaces trunk** для проверки настроек транка.
```
conf t
int fa 0/5
sw m trunk
sw tr native vl 1000
sw tr allowed vl 10,20,30,1000
end
wr
```
```
S1#sh int trunk 
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      1000

Port        Vlans allowed on trunk
Fa0/1       10,20,30,1000

Port        Vlans allowed and active in management domain
Fa0/1       20,30

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       20,30

```
**Примечание:**Тут стоит заметить, что в выводе нет интерфейса fa0/5, потому что на роутере, который подключен в этот интерейс, выключен порт. Как только порт включится - fa0/5 появится в выводе
```
S1#sh int trunk 
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      1000
Fa0/5       on           802.1q         trunking      1000

Port        Vlans allowed on trunk
Fa0/1       10,20,30,1000
Fa0/5       10,20,30,1000

Port        Vlans allowed and active in management domain
Fa0/1       20,30
Fa0/5       20,30

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       20,30
Fa0/5       20,30
```

Закройте окно настройки.

## Настройте маршрутизацию

### Настройка маршрутизации между сетями VLAN на R1

Откройте окно конфигурации

- - 1. Активируйте интерфейс G0/0/1 на маршрутизаторе.
- - 2.  Настройте подинтерфейсы для каждой VLAN, как указано в таблице IP-адресации. Все подинтерфейсы используют инкапсуляцию 802.1Q. Убедитесь, что подинтерфейс для собственной VLAN не имеет назначенного IP-адреса. Включите описание для каждого подинтерфейса.
- - 3.  Настройте интерфейс Loopback 1 на R1 с адресацией из приведенной выше таблицы.
- - 4.  С помощью команды **show ip interface brief** проверьте конфигурацию подынтерфейса.
```
conf t
int gi 0/0/1
no sh
int gi 0/0/1.20
encap dot 20
ip addr 10.20.0.1 255.255.255.0
desc Management
no sh
int gi 0/0/1.30
encap dot 30
ip addr 10.30.0.1 255.255.255.0
desc Operations
no sh
int gi 0/0/1.40
encap dot 40
ip addr 10.40.0.1 255.255.255.0
desc Sales
no sh
int gi 0/0/1.1000
desc PatkingLot
no sh
int loop 1
ip addr 172.16.1.1 255.255.255.0
no sh
end
wr
```
```
R1#sh ip int br
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0/0   unassigned      YES unset  administratively down down 
GigabitEthernet0/0/1   unassigned      YES unset  up                    up 
GigabitEthernet0/0/1.2010.20.0.1       YES manual up                    up 
GigabitEthernet0/0/1.3010.30.0.1       YES manual up                    up 
GigabitEthernet0/0/1.4010.40.0.1       YES manual up                    up 
GigabitEthernet0/0/1.1000unassigned      YES unset  up                    up 
GigabitEthernet0/0/2   unassigned      YES unset  administratively down down 
Loopback1              172.16.1.1      YES manual up                    up 
Vlan1                  unassigned      YES unset  administratively down down
```

### Настройка интерфейса R2 g0/0/1 с использованием адреса из таблицы и маршрута по умолчанию с адресом следующего перехода 10.20.0.1
```
conf t
int gi 0/0/1
ip addr 10.20.0.4 255.255.255.0
no sh
ex
ip route 0.0.0.0 0.0.0.0 10.20.0.1
end
wr
```

Закройте окно настройки.

## Настройте удаленный доступ

### Настройте все сетевые устройства для базовой поддержки SSH

Откройте окно конфигурации

- - 1. Создайте локального пользователя с именем пользователя SSHadmin и зашифрованным паролем $cisco123!
- - 2.  Используйте **ccna-lab.com** в качестве доменного имени.
- - 3.  Генерируйте криптоключи с помощью 1024 битного модуля.
- - 4.  Настройте первые пять линий VTY на каждом устройстве, чтобы поддерживать только SSH-соединения и с локальной аутентификацией.
```
conf t
username SSHadmin privilege 15 secret 5 $1$mERr$jnsDknF4Wwkgx2tzKw49w1

ip domain-name ccna-lab.com
crypto key generate rsa general-keys modulus 1024
line vty 0 4
logging synchronous
login local
transport input ssh
end
wr
```
### Включите защищенные веб-службы с проверкой подлинности на R1

- - - 1. Включите сервер HTTPS на R1.

R1(config)# **ip http secure-server**

- - - 1. Настройте R1 для проверки подлинности пользователей, пытающихся подключиться к веб-серверу.

R1(config)# **ip http authentication local**

**К сожалению, в Cisco Packet Tracer нет http сервера на cisco. Вместо него я поставил сервер с адресами 10.20.0.20 и 172.16.2.20 с https сервером. Подключил его к S1 в порт fa0/20. Схема подключения:**
![image](https://github.com/user-attachments/assets/7a928504-449b-4c95-af7a-e9c6bf1e72f4)

**Конфиг S1**
```
conf t
int fa 0/20
sw m ac
sw ac vl 20
desc WEB
no sh
end
wr
```

Закройте окно настройки.

## Проверка подключения

### Настройте узлы ПК

Адреса ПК можно посмотреть в таблице адресации.

### Выполните следующие тесты. Эхозапрос должен пройти успешно

- - - 1. **Примечание.** Возможно, вам придется отключить брандмауэр ПК для работы ping

| От  | Протокол | Назначение |
| --- | --- | --- |
| PC-A | Ping | 10.40.0.10 |
| PC-A | Ping | 10.20.0.1 |
| PC-B | Ping | 10.30.0.10 |
| PC-B | Ping | 10.20.0.1 |
| PC-B | Ping | 172.16.1.1 |
| PC-B | HTTPS | 10.20.0.1 |
| PC-B | HTTPS | 172.16.1.1 |
| PC-B | SSH | 10.20.0.1 |
| PC-B | SSH | 172.16.1.1 |

**По заданию выше в магистральный канал нужно было разрешить vlan 10, 20, 30 и 1000, поэтому эхозапрос на 10.40.0.10 не проходит. Так как ожидается успешный ответ - добаляю vlan 40 в магистральный канал командой sw tr allowed vl add 40**
```
C:\>ping 10.40.0.10
Pinging 10.40.0.10 with 32 bytes of data:
Reply from 10.40.0.10: bytes=32 time=15ms TTL=127
```
```
C:\>ping 10.20.0.1
Pinging 10.20.0.1 with 32 bytes of data:
Reply from 10.20.0.1: bytes=32 time=1ms TTL=255
```
```
C:\>ping 10.30.0.10
Pinging 10.30.0.10 with 32 bytes of data:
Reply from 10.30.0.10: bytes=32 time<1ms TTL=127
```
```
C:\>ping 10.20.0.1
Pinging 10.20.0.1 with 32 bytes of data:
Reply from 10.20.0.1: bytes=32 time<1ms TTL=255
```
```
C:\>ping 172.16.1.1
Pinging 172.16.1.1 with 32 bytes of data:
Reply from 172.16.1.1: bytes=32 time<1ms TTL=255
```
![image](https://github.com/user-attachments/assets/2f8b0dd1-9b4e-4625-bd41-9c5d286bb43b)

Для имитации веб на loopback
```
R1(config)#int gi 0/0/1.172
R1(config-subif)#ip addr 172.16.2.1 255.255.255.0
R1(config-subif)#no sh
```
```
S1#conf t
S1(config)#vlan 172
S1(config-vlan)#name web-in-virt-loop
S1(config-vlan)#ex
S1(config)#int fa 0/5
S1(config-if)#sw tr all vl add 172
S1(config-if)#int fa0/21
S1(config-if)#sw m ac
S1(config-if)#sw ac vl 172
S1(config-if)#no sh
```
![image](https://github.com/user-attachments/assets/68f500ba-0627-4e60-b491-c6fed9c4cecc)

![image](https://github.com/user-attachments/assets/24ed59bb-0f27-45ff-8314-c03d08795a94)

![image](https://github.com/user-attachments/assets/bd8f6cc8-fdcb-4534-9bec-1ac2c95474af)


## Настройка и проверка списков контроля доступа (ACL)

При проверке базового подключения компания требует реализации следующих политик безопасности:

**Политика**1\. Сеть Sales не может использовать SSH в сети Management (но в другие сети SSH разрешен).

**Политика 2**. Сеть Sales не имеет доступа к IP-адресам в сети Management с помощью любого веб-протокола (HTTP/HTTPS). Сеть Sales также не имеет доступа к интерфейсам R1 с помощью любого веб-протокола. Разрешён весь другой веб-трафик (обратите внимание — Сеть Sales может получить доступ к интерфейсу Loopback 1 на R1).

**Политика**3\. Сеть Sales не может отправлять эхо-запросы ICMP в сети Operations или Management. Разрешены эхо-запросы ICMP к другим адресатам.

**Политика 4**: Cеть Operations не может отправлять ICMP эхозапросы в сеть Sales. Разрешены эхо-запросы ICMP к другим адресатам.

### Проанализируйте требования к сети и политике безопасности для планирования реализации ACL

### Разработка и применение расширенных списков доступа, которые будут соответствовать требованиям политики безопасности

Откройте окно конфигурации
```
conf t
ip access-list extended 101
50 deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq 22
60 deny icmp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 echo
40 deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq www
41 deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq 443
70 deny tcp 10.40.0.0 0.0.0.255 10.30.0.0 0.0.0.255 eq www
71 deny tcp 10.40.0.0 0.0.0.255 10.30.0.0 0.0.0.255 eq 443
80 deny icmp 10.40.0.0 0.0.0.255 10.30.0.0 0.0.0.255 echo
80 deny icmp 10.30.0.0 0.0.0.255 10.40.0.0 0.0.0.255 echo
100 permit ip any any
```
```
R1(config-ext-nacl)#do sh acc
Extended IP access list 101
    40 deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq www
    41 deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq 443
    50 deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq 22
    60 deny icmp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 echo
    70 deny tcp 10.40.0.0 0.0.0.255 10.30.0.0 0.0.0.255 eq www
    71 deny tcp 10.40.0.0 0.0.0.255 10.30.0.0 0.0.0.255 eq 443
    80 deny icmp 10.40.0.0 0.0.0.255 10.30.0.0 0.0.0.255 echo
    80 deny icmp 10.30.0.0 0.0.0.255 10.40.0.0 0.0.0.255 echo
    100 permit ip any any
```
```
conf t
int gi 0/0/1.20
ip access-group 101 in
int gi 0/0/1.30
ip access-group 101 in
int gi 0/0/1.40
ip access-group 101 in
int gi 0/0/1.172
ip access-group 101 in
do wr
```
Закройте окно настройки.

### Убедитесь, что политики безопасности применяются развернутыми списками доступа

Выполните следующие тесты. Ожидаемые результаты показаны в таблице:

| От  | Протокол | Назначение | Результат |
| --- | --- | --- | --- |
| PC-A | Ping | 10.40.0.10 | Сбой |
| PC-A | Ping | 10.20.0.1 | Успех |
| PC-B | Ping | 10.30.0.10 | Сбой |
| PC-B | Ping | 10.20.0.1 | Сбой |
| PC-B | Ping | 172.16.1.1 | Успех |
| PC-B | HTTPS | 10.20.0.1 | Сбой |
| PC-B | HTTPS | 172.16.1.1 | Успех |
| PC-B | SSH | 10.20.0.4 | Сбой |
| PC-B | SSH | 172.16.1.1 | Успех |

![image](https://github.com/user-attachments/assets/1ff38f0e-40bc-4f61-b4b8-53339810b251)

![image](https://github.com/user-attachments/assets/667c9c86-499c-4707-8056-669fbbd87ef6)

![image](https://github.com/user-attachments/assets/88c1322c-1425-4fa7-a1ab-77d266861e94)

![image](https://github.com/user-attachments/assets/dd745ea2-968c-4d2d-982e-e8a2b3d2c152)

![image](https://github.com/user-attachments/assets/3fdb0610-0b33-4a1a-9015-cd68bdc13ba7)

Конец документа
