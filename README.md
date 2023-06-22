# osi7-ipv6-troubleshooting
OSI 7 - IPv6 - Troubleshooting

# Высокоуровневые сетевые протоколы

**Прикладной уровень** – последний уровень модели OSI, обеспечивает обеспечивает взаимодействие сети и пользователя.
- Примеры протоколов: HTTP; DNS; IMAP; SSH; FTP.

**XMPP (eXtensible Messaging and Presence Protocol)** – расширяемый протокол обмена сообщениями и информацией о присутствии. В данный момент используется в основном для создания приватных серверов

**FTP (File transfer protocol)** – протокол передачи файлов. Реализует обмен файлов между клиентом и сервером. Есть поддержка активного и пассивного режима.

Состоит из 2-х процессов:
- управляющий процесс;
- процесс передачи данных.

**Основы шифрования**

Шифрование с открытым ключом
- У каждого участника есть 2 ключа: публичный и приватный.
- Публичный ключ может только шифровать сообщения.
- Приватный ключ может только расшифровывать сообщение, и только те сообщения что были зашифрованы связанным с ним приватным ключом.
- Асинхронное шифрование является медленным и ресурсозатратным, поэтому используется только для гарантии безопасного обмена общим сеансовым ключом.
- Сеансовый ключ может зашифровывать и расшифровывать.
- Создается на время в момент рукопожатия.

SSL рукопожатие
- (Клиент) Запрос на поделючение
- (Сервер) Отправка публичного ключа
- (Клиент)Шифрование с помощью публичного ключа сгенерированного публичного ключа
- (Сервер) Расшифровка сгенерированного ключа и его использование для обмена данными

Генерирование ключей SSH
- устанавливаем OpenSSH сервер (Ubuntu): sudo apt-get install openssh-server
- генерируем ключ: ssh-keygen -t rsa
- получаем файлы:
  - /home/user/.ssh/id_rsa
  - /home/user/.ssh/id_rsa.pub
- записываем публичный ключ: cat /home/user/.ssh/id_rsa.pub >> /home/user/.ssh/authorized_keys
- пробуем подключиться с использованием ключа: ssh -i /home/user/.ssh/id_rsa localhost

**SSL Сертификаты**

Цепочка доверия
- Корневой сертификат – сертификат, с правом подписи. Генерируется изначально.
- Промежуточный сертификат – обычно сертификат с правом подписи. подписанный корневым, выдается компаниям посредникам между ЦС и конечным пользователям.
- Конечный сертификат – подтверждает владение неким цифровым активом (например, доменом).

Реализация получения конечного сертификата:
- Публичный ключ (PUB2) отправляется в ЦС.
- ЦС создает сертификат в котором есть зашифрованный приватным ключом PRIV1 ключ PUB2.
- Любой, кто хочет проверить подлинность сертификата, может взять ключ PUB1, расшифровать сертификат и получить PUB2.

ACME - Описывает методы тестирования домена и приватных / публичных ключей для запрашиваемого сертификата. Позволяет автоматизировать процесс получения сертификата.


# Траблшутинг

**Troubleshooting (устранение неполадок, работа над проблемой)** — форма решения проблем, часто применяемая к ремонту неработающих устройств или процессов. Представляет собой систематический, опосредованный определённой логикой поиск источника проблемы с целью её решения.

Траблшутинг как поиск и устранение неисправностей необходим для поддержания и развития сложных систем, где проблема может иметь множество различных причин.

Основные проблемы в сетях
- физические (L1) – кабель отключен или повреждён;
- connectivity (L1, L2) – порт на оборудовании может быть не настроен, заблокирован или отключен;
- некорректная настройка (L2, L3, L4) – некорректный IP-адрес, маршрут, шлюз, блокировка на стороне firewall’а, и т.д.;
- некорректная работа ПО (L5-L7) – несоответствие версий ПО, выключенное ПО, неправильно настроенный порт, и т.д.;
- загрузка канала – попытка передать через канал большее количество информации, чем возможно;
- проблема на стороне провайдера

Л – логика. Л – локализация проблемы.
- Если один клиент жалуется на отсутствие доступа к разным сервисам – скорее всего проблема только у него (компьютер, IP-адрес, сеть, ПО).
- Если много клиентов жалуется на отсутствие доступа к одному сервису – скорее всего проблема в сервисе.
- Если много клиентов жалуется на отсутствие доступа к разным сервисам – скорее всего или глобальные проблемы в локальной сети, или проблемы у провайдера.

**Физические проблемы** - Здесь всё достаточно просто – что бы ни случилось, для работы всегоостального должна быть связность на физическом уровне модели OSI (Layer 1).
- Если не работает физическое подключение – не будет работатьничего!

Вопросы, на которые нужно иметь положительный ответ:
- Есть ли электричество на оборудовании? Включена ли VM?
- Подключен ли кабель?
- Если подключен – уверены ли вы, что он целый?

Проблемы с connectivity (L1, L2)
Здесь всё ещё достаточно просто, хотя немного сложнее – всё может быть подключено, но всё равно не работать.

Вопросы, на которые нужно иметь положительный ответ:
- Если кабель подключен и в порядке – горит ли лампочка (есть ли «линк»)?
- Не заблокирован ли порт в настройках коммутатора / сервера?
- Не заблокирован ли MAC-адрес на коммутаторе?

Команды поиска проблем на L1, L2
- ip link - eth1 – NO-CARRIER означает, что сетевой разъем не обнаруживает сигнал на линии. UP / state DOWN – интерфейс работает / не подключён (LOWER_UP отсутствует)

**Некорректная настройка (L2, L3, L4)**
Вопросы, на которые нужно иметь положительный ответ:
- Есть ли связь на L2? Вижу ли я компьютеры внутри локальной сети
- Есть ли связь на L3?
- Много что работает, не работает связь именно с этим IP-адресом. Тестируем с помощью arp, ping, trace.
- Есть ли связь на L4? Хост доступен, однако не работает конкретный сервис. Тестируем с помощью telnet со стороны клиента, ss / netstat со стороны сервера, просматриваем правила firewall’а

Команды поиска проблем на L2 (arp, arping)
- Просмотр arp (связь L2 и L3)
  - ip neigh show dev eth1
  - arp -i eth1
- Опрос устройства на L2
  - sudo arping -c 1 10.0.2.3
- Просмотр трафика L2
  - sudo tcpdump -i any arp -nn -A -e  (С опцией -e программа tcpdump будет печатать заголовки канального уровня в каждой выведенной строке. С опцией -A команда tcpdump будет отображать на экране содержимое пакетов в формате ASCII. Опция -nn отображает порты и IP-адреса цифрами вместо имён (localhost, ssh, http, и т.д.))
- ip -4 address
- ip route list
- Опрос хоста на L3
  - ping -c 1 netology.ru
- Проверка маршрута до хоста L3
  - traceroute -n ya.ru
  - ip route list
  - cat /etc/resolv.conf 
- Поиск проблемы на L4
  - telnet ya.ru 80
  - curl -v http://ya.ru:80

Если нет связи по нужному нам порту, при том что хост доступен, пытаемся подключиться ещё куда-то по тому же порту (TCP / UDP), чтобы понять, не блокируется ли подключение нашим сетевым администратором на пограничном роутере.

Также просматриваем правила нашего firewall’а
- sudo iptables -L -v

В зависимости от того что покажет tcpdump на разных хостах, мы сможем делать дальнейшие выводы о локализации проблемы:
```
root@netology1:~# tcpdump -nn -i eth1
10:34:38.393610 IP 172.28.128.10.50700 > 172.28.128.60.80: Flags [S]...
10:34:39.423744 IP 172.28.128.10.50700 > 172.28.128.60.80: Flags [S]...
root@netology2:~# tcpdump -nn -i eth1 # (вариант 1)
10:34:37.621593 IP 172.28.128.10.50700 > 172.28.128.60.80: Flags [S]...
10:34:38.652240 IP 172.28.128.10.50700 > 172.28.128.60.80: Flags [S]...
root@netology2:~# tcpdump -nn -i eth1 # (вариант 2)
```
- Вариант 1 говорит о том, что TCP сегменты на хост netology2приходят, но не генерируются ответные.
- Вариант 2 говорит о том, что сегменты до хоста netology2 не доходят.

**Некорректная работа ПО (L5-L7)**

Здесь многое аналогично L4, т.к. если диагностировать проблему выше 3-го уровня, то со стороны клиента это будет выглядеть как «связь в принципе есть, однако ответ на мой запрос по порту 80 не приходит». И понять, в чём именно причина, не имея доступа к серверной части, иногда сложно.

Вопросы, на которые нужно иметь положительный ответ:
- Есть ли связь на L4 по тем же портам с другими хостами?
- Корректно ли работает ПО со стороны клиента?
- Корректно ли работает ПО со стороны сервера?

Есть ли связь на L4/L7 с другими хостами?
- Если связь у клиента по тем же популярным портам (80, 443, 22, 23, и т. д.) с другими хостами есть, это означает, что с клиентской стороны скорее всего всё в порядке.
- При этом без доступа к серверу понять причину будет невозможно, но попробовать можно.
- Если речь идёт о подключении к высокодоступным сервисам (Yandex, Google, и т.д.) – проблема чаще на стороне пользователя или провайдера. Можно попробовать изменить внешний IP-адрес и проверить ещё раз.

Если есть доступ к серверу
- запущена ли служба (ps aux);
  - ps – утилита для сбора информации о запущенных процессах
- слушает ли она нужный порт (ss, netstat);
  - socket statistics – наиболее актуальная утилита для сбора информации о сокетах, в частности сетевых сокетах. Аналог netstat.
  - ss -4 state listening state unconnected -n | column -t
  - ss state connected sport != :ssh -t | column -t - установленные TCP соединения не на порт SSH
  - lsof – мощная утилита, в том числе для получения информации по сети. Среди прочего он поможет вам узнать, какому процессу принадлежит прослушиваемый порт:
  - sudo lsof -ni :22
- какие ошибки есть в логах (/var/log/*.log, файл зависит от сервиса
  - remote syslog - rsyslog - можно мониторить и собирать логи отдельно
  - kibana - используется для мониторинга и анализа ИТ-инфраструктуры в составе Elastic Stack (ранее ELK Stack), в который помимо нее входят Elasticsearch и Logstash. Logstash отвечает за логирование и поставляет входящий поток данных в Elasticsearch для хранения, классификации и поиска. Kibana, в свою очередь, получает доступ к данным Elasticsearch для их визуализации в различных визуальных форматах, например – в виде информационных панелей (dashboards) с различными видами диаграмм
 
**Загрузка канала** - Если пинг от клиента к серверу стабильно высокий, плюс «тормозит» не только этот сервис – можем попробовать сделать вывод о том, что канал загружен. Если же медленно работает только один сервис – проблема на стороне сервиса.
- iftop
- bmon
- sampler
- gping

Проблема не на нашей стороне
- Если trace показывает потери или сильное увеличение задержек на каком-то узле вне нашей зоны ответственности – мы ничего сделать не можем.
- Если не можем подключиться только к конкретному серверу, к остальным по тем же портам подключаемся.
- Рекомендации – попробовать подключение с другого оператора (с 4g роутера, резервного канала, и т.д.) + пообщаться с сетевым администратором и / или коллегами, может быть недавно вносили какие-то изменения в конфигурационные файлы
- mtr 8.8.8.8
- tracepath -n 8.8.8.8
- iperf3

# IPv6

Зачем нужна замена IPv4?
- Недостаточное количество IP-адресов;
- IPv4 ребует множество дополнительных технологий (VLSM,CIDR,NAT, DHCP) для работы

Преимущества IPv6 перед IPv4
- IPv6 обеспечивает более эффективную маршрутизацию, поскольку значительно уменьшает размер таблицы маршрутизации;
- у нового протокола формат заголовка проще, чем у IPv4;
- обработка пакетов более эффективна, поскольку заголовки пакетов оптимизированы;
- в протокол встроена технология Quality of Service (QoS), которая определяет чувствительные к задержке пакеты;
- более упрощенные задачи маршрутизаторов по сравнению с IPv4;
- IPv6 обеспечивает большую полезную нагрузку, чем IPv4

Размер адреса IPv6 – 128 бит:
- адрес записывается в шестнадцатеричном формате;
- 8 групп по 4 разряда, разделенные двоеточиями.

**Маска адреса IPv6**
Как и в IPv4 адрес делится на две части:
- Network ID – общий для всех узлов в канальной среде;
- Interface ID – идентификатор узла в канале.
- Стандартная (ожидаемая) маска для хостов – /64, формат записи префиксный.

**SLAAC (Stateless Address Autoconfiguration, автонастройка адреса без сохранения состояния)** – это механизм автоконфигурации узла, который используется для автоматического получения IP адреса и сетевого префикса узлом, без использования DHCPv6-сервера, или совместно с ним. SLAAC основан на новых возможностях ICMPv6
- Только SLAAC - Маршрутизатор выдаёт подсеть, префикс и адрес шлюза. Другую информацию устройства не получают.
- SLAAC и DHCPv6 - Маршрутизатор выдаёт подсеть, префикс и адрес шлюза, а отдельный DHCPv6-сервер выдаёт дополнительную информацию: опции, маршруты, адреса DNS-серверов и т.д.
- Только DHCPv6 - Устройство не использует RA от маршрутизатора, а обращается к DHCPv6- серверу, который предоставляет всю необходимую информацию, включая адрес, шлюз, префикс, DNS-сервера и т.д

Механизм EUI-64 (Extended Unique Identifier) позволяет хосту в IPv6 самостоятельно генерировать себе идентификатор интерфейса – то есть вторую половину IPv6 адреса.

Порядок работы EUI-64:
- MAC адрес делится на две части по 24 бита каждая;
- между этими частями вставляются шестнадцатеричные цифры FFFE;
- седьмой по порядку бит полученного адреса меняется на противоположный (1 – на 0, 0 – на единицу)

Виды трафика в IP
- Unicast – одноадресная передача между двумя конечными узлами;
- Multicast – используется, когда нужно передать какую-то информацию не всем узлам а какой-то определенной группе, при этом узлы группы могут находиться в разных канальных средах;
- Anycast – передача единственному узлу из группы когда есть варианты прохождения запроса; задача выбрать ближайший;
- Broadcast – многоадресная передача группе адресатов; ограничена канальной средой

Типы адресов IPv6
- Global Unicast – аналог «белых» IP адресов для работы в интернете; Пример: 2000::/3
- Unique Local Unicast – локальные адреса, которые не должны попадать в интернет (при этом должны быть уникальны в пределах всего интернета); Пример: fc00::/7
- Link Local Unicast – адреса, доступные в пределах одной канальной среды; Пример: fe80::/10
- Multicast - Пример: ff00::/8
- Специальные адреса - Пример: ::1/128,::/128,...

**Структура глобального IPv6 адреса**

![global1](https://github.com/joos-network/osi7-ipv6-troubleshooting/blob/main/global62.png)
![global2](https://github.com/joos-network/osi7-ipv6-troubleshooting/blob/main/global61.png)

**Структура локального IPv6 адреса**
![local1](https://github.com/joos-network/osi7-ipv6-troubleshooting/blob/main/local6.png)

Векторы атак
- сканирование подсети может затянуться на годы, но вместо сканирования атака может быть произведена на Neighbor Discovery;
- порты на IPv6 должны защищаться так же как в IPv4, либо же стоит отключить их;
- NAT не делает IPv4 безопаснее, а его отсутствие не делает IPv6 более небезопасным;
- IPv6 также подвержен DOS атакам, но за счет отсутствия широковещания проявляет себя лучше при DDOS типа smurf.
