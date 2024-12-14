# Лабораторная работа. Развертывание коммутируемой сети с резервными каналами

## Топология

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab07/pics/pic_base.png)

## Таблица адресации

| Устройство | Интерфейс | IP-адрес | Маска подсети |
| --- | --- | --- | --- |
| S1  | VLAN 1 | 192.168.1.1 | 255.255.255.0 |
| S2  | VLAN 1 | 192.168.1.2 | 255.255.255.0 |
| S3  | VLAN 1 | 192.168.1.3 | 255.255.255.0 |

# Цели

Часть 1. Создание сети и настройка основных параметров устройства

Часть 2. Выбор корневого моста

Часть 3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов

Часть 4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов

# Решение

## Часть 1.Создание сети и настройка основных параметров устройства

## Шаг 1. Создайте сеть согласно топологии

Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab07/pics/pic_1.png)

## Шаг 2. Выполните инициализацию и перезагрузку коммутаторов

## Шаг 3. Настройте базовые параметры каждого коммутатора

1. Отключите поиск DNS.
2. Присвойте имена устройствам в соответствии с топологией.
3. Назначьте class в качестве зашифрованного пароля доступа к привилегированному режиму.
4. Назначьте cisco в качестве паролей консоли и VTY и активируйте вход для консоли и VTY каналов.
5. Настройте logging synchronous для консольного канала.
6. Настройте баннерное сообщение дня (MOTD) для предупреждения пользователей о запрете несанкционированного доступа.
7. Задайте IP-адрес, указанный в таблице адресации для VLAN 1 на всех коммутаторах.
8. Скопируйте текущую конфигурацию в файл загрузочной конфигурации.
```
Switch>en
Switch#conf t
Switch(config)#no ip domain-lookup
Switch(config)#hostname S1
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
S1(config)#banner motd #UNAUTORIZED ACCESS PROHIBITED#
S1(config)#int vlan 1
S1(config-if)#ip add 192.168.1.1 255.255.255.0
S1(config-if)#no sh
S1(config-if)#
S1(config-if)#do wr
Building configuration...
[OK]
```
## Шаг 4. Проверьте связь

Проверьте способность компьютеров обмениваться эхо-запросами.

Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S2? \___\__Да_\___\___\__

Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S3? \___\__Да_\___\___\__

Успешно ли выполняется эхо-запрос от коммутатора S2 на коммутатор S3? \___\__Да_\___\___\__

# Часть 2. Определение корневого моста

## Шаг 1. Отключите все порты на коммутаторах
```
S3(config-if)#int ra fa 0/1-24, gi 0/1-2
S3(config-if-range)#sh
```
## Шаг 2. Настройте подключенные порты в качестве транковых
```
S3(config-if-range)#int range fa 0/1-4
S3(config-if-range)#sw m tr
```
## Шаг 3. Включите порты F0/2 и F0/4 на всех коммутаторах
```
S3(config-if-range)#int range fa 0/2, fa0/4
S3(config-if-range)#no sh
```
## Шаг 4. Отобразите данные протокола spanning-tree
### S1
```
S1#sh spanning-tree
VLAN0001
Spanning tree enabled protocol ieee
Root ID Priority 32769
Address 0002.16C7.8951
Cost 19
Port 4(FastEthernet0/4)
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec
Bridge ID Priority 32769 (priority 32768 sys-id-ext 1)
Address 0005.5E8E.3E8B
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec
Aging Time 20
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Desg FWD 19        128.2    P2p
Fa0/4            Root LRN 19        128.4    P2p
```
### S2
```
S2#sh spanning-tree
VLAN0001
Spanning tree enabled protocol ieee
Root ID Priority 32769
Address 0002.16C7.8951
Cost 19
Port 4(FastEthernet0/4)
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec
Bridge ID Priority 32769 (priority 32768 sys-id-ext 1)
Address 00D0.D35C.6903
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec
Aging Time 20
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Altn BLK 19        128.2    P2p
Fa0/4            Root FWD 19        128.4 P2p
```
### S3
```
S3#sh span
VLAN0001
Spanning tree enabled protocol ieee
Root ID Priority 32769
Address 0002.16C7.8951
This bridge is the root
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec
Bridge ID Priority 32769 (priority 32768 sys-id-ext 1)
Address 0002.16C7.8951
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec
Aging Time 20
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Desg FWD 19        128.2    P2p
Fa0/4            Desg FWD 19        128.4    P2p
```
В схему ниже запишите роль и состояние (Sts) активных портов на каждом коммутаторе в топологии.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab07/pics/pic_schema.png)

С учетом выходных данных, поступающих с коммутаторов, ответьте на следующие вопросы.

Вопрос: Какой коммутатор является корневым мостом?                                                                       
Ответ: S3

Вопрос: Почему этот коммутатор был выбран протоколом spanning-tree в качестве корневого моста?                                              
Ответ: Потому что у S3 наименьший mac адрес

Вопрос: Какие порты на коммутаторе являются корневыми портами?                                               
Ответ: У корневого коммутатора нет корневых портов. На S1 корневым портом является Fa0/4, на S2 Fa0/4

Вопрос: Какие порты на коммутаторе являются назначенными портами?                                                   
Ответ: У корневого коммутатора все порты назначенные. На S1 назначенным является порт Fa0/2

Вопрос: Какой порт отображается в качестве альтернативного и в настоящее время заблокирован?                                 
Ответ: Порт Fa0/2 коммутатора S2

Вопрос: Почему протокол spanning-tree выбрал этот порт в качестве невыделенного (заблокированного) порта?                             
Ответ: Потому что марщрут через этот порт до корневого коммутатора S1 самый длинный и будет использоваться лишь в случае, если связь по корневому порту пропадёт

# Часть 3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов

## Шаг 1. Определите коммутатор с заблокированным портом
```
S2#sh span
VLAN0001
Spanning tree enabled protocol ieee
Root ID Priority 32769
Address 0002.16C7.8951
Cost 19
Port 4(FastEthernet0/4)
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec
Bridge ID Priority 32769 (priority 32768 sys-id-ext 1)
Address 00D0.D35C.6903
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec
Aging Time 20
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Altn BLK 19        128.2    P2p
Fa0/4            Root FWD 19        128.4    P2p
```
## Шаг 2. Измените стоимость порта

Помимо заблокированного порта, единственным активным портом на этом коммутаторе является порт, выделенный в качестве порта корневого моста. Уменьшите стоимость этого порта корневого моста до 18, выполнив команду spanning-tree vlan 1 cost 18 режима конфигурации интерфейса.
```
S2(config-if)#int fa0/4
S2(config-if)#spanning-tree vlan 1 cost 18
```
## Шаг 3. Просмотрите изменения протокола spanning-tree

Повторно выполните команду show spanning-tree на обоих коммутаторах некорневого моста. Обратите внимание, что ранее заблокированный порт (S2 – F0/2) теперь является назначенным портом, и протокол spanning-tree теперь блокирует порт на другом коммутаторе некорневого моста (S3 – F0/4).

### S1
```
S1#sh span
VLAN0001
Spanning tree enabled protocol ieee
Root ID Priority 32769
Address 0002.16C7.8951
Cost 19
Port 4(FastEthernet0/4)
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec
Bridge ID Priority 32769 (priority 32768 sys-id-ext 1)
Address 0005.5E8E.3E8B
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec
Aging Time 20
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Altn BLK 19        128.2    P2p
Fa0/4            Root FWD 19        128.4    P2p
```
### S2
```
S2(config-if)#do sh span
VLAN0001
Spanning tree enabled protocol ieee
Root ID Priority 32769
Address 0002.16C7.8951
Cost 18
Port 4(FastEthernet0/4)
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec
Bridge ID Priority 32769 (priority 32768 sys-id-ext 1)
Address 00D0.D35C.6903
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec
Aging Time 20
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Desg FWD 19        128.2    P2p
Fa0/4            Root FWD 18        128.4    P2p
```
## Шаг 4. Удалите изменения стоимости порта

1. Выполните команду no spanning-tree vlan 1 cost 18 режима конфигурации интерфейса, чтобы удалить запись стоимости, созданную ранее.
```
S2(config-if)#int fa0/4
S2(config-if)#no spanning-tree vlan 1 cost 18
```
1. Повторно выполните команду show spanning-tree, чтобы подтвердить, что протокол STP сбросил порт на коммутаторе некорневого моста, вернув исходные настройки порта. Протоколу STP требуется примерно 30 секунд, чтобы завершить процесс перевода порта.
```
S2(config-if)#do sh span
VLAN0001
Spanning tree enabled protocol ieee
Root ID Priority 32769
Address 0002.16C7.8951
Cost 19
Port 4(FastEthernet0/4)
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec
Bridge ID Priority 32769 (priority 32768 sys-id-ext 1)
Address 00D0.D35C.6903
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec
Aging Time 20
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Altn BLK 19        128.2    P2p
Fa0/4            Root FWD 19        128.4    P2p
```
# Часть 4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов

1. Включите порты F0/1 и F0/3 на всех коммутаторах.
```
S1(config)#int ra fa 0/1, fa0/3
S1(config-if-range)#no sh
```
1. Подождите 30 секунд, чтобы протокол STP завершил процесс перевода порта, после чего выполните команду show spanning-tree на коммутаторах некорневого моста. Обратите внимание, что порт корневого моста переместился на порт с меньшим номером, связанный с коммутатором корневого моста, и заблокировал предыдущий порт корневого моста.

### S1
```
S1#sh spanning-tree
VLAN0001
Spanning tree enabled protocol ieee
Root ID Priority 32769
Address 0002.16C7.8951
Cost 19
Port 3(FastEthernet0/3)
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec
Bridge ID Priority 32769 (priority 32768 sys-id-ext 1)
Address 0005.5E8E.3E8B
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec
Aging Time 20
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/1            Desg FWD 19        128.1 P2p
Fa0/2            Desg FWD 19        128.2 P2p
Fa0/3            Root LRN 19        128.3 P2p
Fa0/4            Altn BLK 19        128.4 P2p
```
### S2
```
S2#sh span
VLAN0001
Spanning tree enabled protocol ieee
Root ID Priority 32769
Address 0002.16C7.8951
Cost 19
Port 3(FastEthernet0/3)
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec
Bridge ID Priority 32769 (priority 32768 sys-id-ext 1)
Address 00D0.D35C.6903
Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec
Aging Time 20
Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/1            Altn BLK 19        128.1 P2p
Fa0/2            Altn BLK 19        128.2 P2p
Fa0/4            Altn BLK 19        128.4 P2p
Fa0/3            Root FWD 19        128.3 P2p
```
Вопрос: Какой порт выбран протоколом STP в качестве порта корневого моста на каждом коммутаторе некорневого моста?                              
Ответ: На S1 порт Fa0/3, на S2 порт Fa0/3


Вопрос: Почему протокол STP выбрал эти порты в качестве портов корневого моста на этих коммутаторах?                                          
Ответ: Потому что они напрямую подключены к корневому коммутатору и имеют наименьший mac

# Вопросы для повторения

1. Какое значение протокол STP использует первым после выбора корневого моста, чтобы определить выбор порта?                            
Ответ: Протокол STP использует стоимость (или метрику) порта для определения выбора порта после выбора корневого моста


1. Если первое значение на двух портах одинаково, какое следующее значение будет использовать протокол STP при выборе порта?
Ответ: Если первое значение (стоимость порта) на двух портах одинаково при выборе порта в протоколе STP, то протокол будет использовать идентификатор порта (Port ID или mac) в качестве следующего критерия для выбора активного порта


1. Если оба значения на двух портах равны, каким будет следующее значение, которое использует протокол STP при выборе порта?
Ответ: Номер порта: В случае равенства Port ID, STP сравнивает номера портов (где меньший номер порта считается более предпочтительным).
