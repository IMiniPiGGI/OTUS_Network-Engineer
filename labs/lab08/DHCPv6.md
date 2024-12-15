
# Лабраторная работа - Настройка DHCPv6

## Топология

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab08/pics/pic_ipv6_base.png)

## Таблица адресации

| Устройство | Интерфейс | IPv6-адрес |
| --- | --- | --- |
| R1  | G0/0/0 | 2001:db8:acad:2::1/64 |
| R1  | G0/0/0 | fe80::1 |
| R1  | G0/0/1 | 2001:db8:acad:1::1/64 |
| R1  | G0/0/1 | fe80::1 |
| R2  | G0/0/0 | 2001:db8:acad:2::2/64 |
| R2  | G0/0 | fe80::2 |
| R2  | G0/0/1 | 2001:db8:acad:3::1/64 |
| R2  | G0/0/1 | fe80::1 |
| PC-A | NIC | DHCP |
| PC-B | NIC | DHCP |

## Задачи

Часть 1. Создание сети и настройка основных параметров устройства

Часть 2. Проверка назначения адреса SLAAC от R1

Часть 3. Настройка и проверка сервера DHCPv6 без гражданства на R1

Часть 4. Настройка и проверка состояния DHCPv6 сервера на R1

Часть 5. Настройка и проверка DHCPv6 Relay на R2

# Решение

## Часть 1. Создание сети и настройка основных параметров устройства

### Шаг 1. Создайте сеть согласно топологии

Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab08/pics/pic_ipv6_1.png)

### Шаг 2. Настройте базовые параметры каждого коммутатора. (необязательно)

1. Присвойте коммутатору имя устройства.
2. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
3. Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.
4. Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.
5. Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.
6. Зашифруйте открытые пароли.
7. Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
8. Отключите все неиспользуемые порты.
9. Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```
Switch>en
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
S1#clock set 14:03 15 Dec 2024
S1#conf t
S1(config)#int range fa0/1-4,fa0/7-24,gi0/1-2
S1(config-if-range)#sh
S1#conf t
S1(config)#int range fa0/1-4,fa0/7-24,gi0/1-2
S1(config-if-range)#sh
S1(config-if-range)#end
S1#wr
```
### Шаг 3. Произведите базовую настройку маршрутизаторов

1. Назначьте маршрутизатору имя устройства.
2. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
3. Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.
4. Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.
5. Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.
6. Зашифруйте открытые пароли.
7. Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
8. Активация IPv6-маршрутизации
9. Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```
Router>en
Router#conf t
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
R1(config)#banner motd #UNAUTORIZED ACCESS PROHIBITED!!!#
R1(config)#service pass
R1(config)#exit
R1#clock set 14:19 15 Dec 2024
R1#conf t
R1(config)#ipv6 unicast-routing
R1(config)#do wr
```
### Шаг 4. Настройка интерфейсов и маршрутизации для обоих маршрутизаторов

1. Настройте интерфейсы G0/0/0 и G0/1 на R1 и R2 с адресами IPv6, указанными в таблице выше.
```
R1(config)#int gi 0/0/0
R1(config-if)#ipv6 address 2001:db8:acad:2::1/64
R1(config-if)#ipv6 address FE80::1 link-local
R1(config-if)#int gi 0/0/1
R1(config-if)#ipv6 add 2001:db8:acad:1::1/64
R1(config-if)#ipv6 add fe80::1 link-local
R1(config-if)# no sh
```
1. Настройте маршрут по умолчанию на каждом маршрутизаторе, который указывает на IP-адрес G0/0/0 на другом маршрутизаторе.
```
R1(config)#ipv6 route ::0/0 2001:db8:acad:2::2
R2(config)#ipv6 route ::0/0 2001:db8:acad:2::1
```
1. Убедитесь, что маршрутизация работает с помощью пинга адреса G0/0/1 R2 из R1

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab08/pics/pic_ipv6_2.png)

1. Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```
R1#wr
```
## Часть 2. Проверка назначения адреса SLAAC от R1

Включите PC-A и убедитесь, что сетевой адаптер настроен для автоматической настройки IPv6.

Через несколько минут результаты команды ipconfig должны показать, что PC-A присвоил себе адрес из сети 2001:db8:1::/64.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab08/pics/pic_ipv6_3.png)

Вопрос: Откуда взялась часть адреса с идентификатором хоста?

Ответ: Была получена по протоколу SLAAC

## Часть 3. Настройка и проверка сервера DHCPv6 на R1

### Шаг 1. Более подробно изучите конфигурацию PC-A

1. Выполните команду ipconfig /all на PC-A и посмотрите на результат.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab08/pics/pic_ipv6_4.png)

1. Обратите внимание, что основной DNS-суффикс отсутствует. Также обратите внимание, что предоставленные адреса DNS-сервера являются адресами «локального сайта anycast», а не одноадресные адреса, как ожидалось.

### Шаг 2. Настройте R1 для предоставления DHCPv6 без состояния для PC-A

1. Создайте пул DHCP IPv6 на R1 с именем R1-STATELESS. В составе этого пула назначьте адрес DNS-сервера как 2001:db8:acad: :1, а имя домена — как stateless.com.
```
R1(config)# ipv6 dhcp pool R1-STATELESS
R1(config-dhcp)# dns-server 2001:db8:acad::254
R1(config-dhcp)# domain-name STATELESS.com
```
1. Настройте интерфейс G0/0/1 на R1, чтобы предоставить флаг конфигурации OTHER для локальной сети R1 и укажите только что созданный пул DHCP в качестве ресурса DHCP для этого интерфейса.
```
R1(config)# interface g0/0/1
R1(config-if)# ipv6 nd other-config-flag
R1(config-if)# ipv6 dhcp server R1-STATELESS
```
1. Сохраните текущую конфигурацию в файл загрузочной конфигурации.
2. Перезапустите PC-A.
3. Проверьте вывод ipconfig /all и обратите внимание на изменения.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab08/pics/pic_ipv6_5.png)

1. Тестирование подключения с помощью пинга IP-адреса интерфейса G0/1 R2.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab08/pics/pic_ipv6_6.png)

## Часть 4. Настройка сервера DHCPv6 с сохранением состояния на R1

1. Создайте пул DHCPv6 на R1 для сети 2001:db8:acad:3:aaa::/80. Это предоставит адреса локальной сети, подключенной к интерфейсу G0/0/1 на R2. В составе пула задайте DNS-сервер 2001:db8:acad: :254 и задайте доменное имя STATEFUL.com.
```
R1(config)# ipv6 dhcp pool R2-STATEFUL
R1(config-dhcp)# address prefix 2001:db8:acad:3:aaa::/80
R1(config-dhcp)# dns-server 2001:db8:acad::254
R1(config-dhcp)# domain-name STATEFUL.com
```
1. Назначьте только что созданный пул DHCPv6 интерфейсу g0/0/0 на R1.
```
R1(config)# interface g0/0/0
R1(config-if)# ipv6 dhcp server R2-STATEFUL
```
## Часть 5. Настройка и проверка ретрансляции DHCPv6 на R2

### Шаг 1. Включите PC-B и проверьте адрес SLAAC, который он генерирует

Обратите внимание на вывод, что используется префикс 2001:db8:acad:3::

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab08/pics/pic_ipv6_7.png)

### Шаг 2. Настройте R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/0/1

1. Настройте команду ipv6 dhcp relay на интерфейсе R2 G0/0/1, указав адрес назначения интерфейса G0/0/0 на R1. Также настройте команду managed-config-flag .

R2 (конфигурация) # int g0/0/1
```
R2(config-if)# ipv6 nd managed-config-flag
R2(config-if)# ipv6 dhcp relay destination 2001:db8:acad:2::1 g0/0/0
```
1. Сохраните конфигурацию.

Шаг 3. Попытка получить адрес IPv6 из DHCPv6 на PC-B.

1. Перезапустите PC-B.
2. Откройте командную строку на PC-B и выполните команду ipconfig /all и проверьте выходные данные, чтобы увидеть результаты операции ретрансляции DHCPv6.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab08/pics/pic_ipv6_8.png)

1. Проверьте подключение с помощью пинга IP-адреса интерфейса R0 G0/0/1.

![](https://github.com/IMiniPiGGI/OTUS_Network-Engineer/blob/Basic-Homework/labs/lab08/pics/pic_ipv6_9.png)

Честно говоря, в Cisco Packet Tracer не смог настроить ipv6 dhcp relay на порту, так как такой команды в cpt в принципе нет, поэтому подключил PC-B к R1, чтобы получить хотя бы какой-то результат. Однако, даже без настроек dns при подключении по топологии, по протоколу SLAAC генерируется IPv6 адрес на PC-B, при помощи которого он может отправлять запросы на Gi0/0/1 R1.
