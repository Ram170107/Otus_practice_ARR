## Цель работы:
- ### Настроить политику маршрутизации в офисе Чокурдах
- ### Распределить трафик между 2 линками

### Описание/Пошаговая инструкция выполнения домашнего задания:
В этой самостоятельной работе мы ожидаем, что вы самостоятельно:

### Настроите политику маршрутизации для сетей офиса.
Сети офиса стерменированы на R28.  На нём настроены саб интерфейсы и прописаны сети.
Маршрутизация между сетями работает благодаря Inter-VLAN_routing.

```
interface Loopback0
 ip address 10.52.0.28 255.255.255.255
!         
interface Ethernet0/0
 description TO_Triada_R26_eth0/1
 ip address 10.0.61.2 255.255.255.252
!         
interface Ethernet0/1
 description TO_Triada_R25_eth0/3
 ip address 10.0.253.2 255.255.255.252
!         
interface Ethernet0/2
 description TO_SW29_eth0/2
 no ip address
!         
interface Ethernet0/2.40
 encapsulation dot1Q 40
 ip address 192.168.40.1 255.255.255.0
!         
interface Ethernet0/2.45
 encapsulation dot1Q 45
 ip address 172.20.45.33 255.255.255.240
!         
interface Ethernet0/2.50
 encapsulation dot1Q 50
 ip address 192.168.50.1 255.255.255.0
!         
interface Ethernet0/3
 no ip address
 shutdown 
```


![](https://github.com/Ram170107/Otus_practice_ARR/blob/ba40f15173f260528d846c620c29528649b90c39/labs/lab_5/%D0%9F%D0%B8%D0%BD%D0%B3%20PC30%20%D0%B4%D0%BE%20%D1%88%D0%BB%D1%8E%D0%B7%D0%B0.png)

> Пинг от PC30 до шлюза
> 

![](https://github.com/Ram170107/Otus_practice_ARR/blob/ba40f15173f260528d846c620c29528649b90c39/labs/lab_5/%D0%9F%D0%B8%D0%BD%D0%B3%20PC31%20%D0%B4%D0%BE%20%D1%88%D0%BB%D1%8E%D0%B7%D0%B0.png)

> Пинг от PC31 до шлюза


![](https://github.com/Ram170107/Otus_practice_ARR/blob/ba40f15173f260528d846c620c29528649b90c39/labs/lab_5/%D0%A2%D0%B5%D1%81%D1%82%20%D0%BF%D0%B8%D0%BD%D0%B3%20%D0%B4%D0%BE%20%D0%9F%D0%9A%20%D0%BE%D1%84%D0%B8%D1%81%D0%BE%D0%B2.png)

> Пинг отшлюза до PC30 и PC31


### Распределите трафик между двумя линками с провайдером.
Для начала я настроил маршруты до R26. 
- От VPC30 маршрут идет через SW29-R28-R26
- От VPC31 маршрут идет через SW29-R28-R25-R26

![](https://github.com/Ram170107/Otus_practice_ARR/blob/4bdc8e30a8796377e253d814850ec47db2824ce7/labs/lab_5/%D0%9C%D0%B0%D1%80%D1%88%D1%80%D1%83%D1%82%D1%8B%20%D0%BE%D1%82%20PC.png)

Также для теста, чтобы на icmp запросы ответы могли доходить обратно, настроил статическую маршрутизацию на R25 и R26:

```
R25#sh ip route 
...
Gateway of last resort is 10.0.252.2 to network 0.0.0.0

S*    0.0.0.0/0 [1/0] via 10.0.252.2
      10.0.0.0/8 is variably subnetted, 9 subnets, 2 masks
C        10.0.231.0/30 is directly connected, Ethernet0/0
...
C        10.52.0.25/32 is directly connected, Loopback0
S     192.168.1.0/24 [1/0] via 10.0.251.2
S     192.168.40.0/24 [1/0] via 10.0.253.2
S     192.168.45.0/24 [1/0] via 10.0.253.2
S     192.168.50.0/24 [1/0] via 10.0.253.2
R25#
```

```
R26#sh ip route
...
      10.0.0.0/8 is variably subnetted, 9 subnets, 2 masks
...
C        10.52.0.26/32 is directly connected, Loopback0
S     192.168.40.0/24 [50/0] via 10.0.61.2
S     192.168.45.0/24 [50/0] via 10.0.61.2
S     192.168.50.0/24 [50/0] via 10.0.61.2
R26#
```
### Распределил маршруты при помощи помощи route-map и PBR настроив их на R28:

```
route-map V40 permit 10
 match ip address 40
 set ip next-hop verify-availability 10.0.61.1 10 track 2
 set ip next-hop verify-availability 10.0.253.1 20 track 1
!         
route-map V50 permit 10
 match ip address 50
 set ip next-hop verify-availability 10.0.253.1 10 track 1
 set ip next-hop verify-availability 10.0.61.1 20 track 2
!         
route-map V45 permit 10
 match ip address 45
 set ip next-hop verify-availability 10.0.253.1 10 track 1
 set ip next-hop verify-availability 10.0.61.1 20 track 2
!         
!         
access-list 40 permit 192.168.40.0 0.0.0.255
access-list 45 permit 192.168.45.0 0.0.0.255
access-list 50 permit 192.168.50.0 0.0.0.255

```

### Настроите отслеживание линка через технологию IP SLA.(только для IPv4)

#### На R28 настроил SLA для отслеживания состояния каналов при помощи эхо запросов привязанных к треку 1 и 2:

```
track 1 ip sla 1 reachability
 delay down 5 up 5
!         
track 2 ip sla 2 reachability
 delay down 5 up 5
...       
interface Loopback0
 ip address 10.52.0.28 255.255.255.255
!         
interface Ethernet0/0
 description TO_Triada_R26_eth0/1
 ip address 10.0.61.2 255.255.255.252
!         
interface Ethernet0/1
 description TO_Triada_R25_eth0/3
 ip address 10.0.253.2 255.255.255.252
!         
interface Ethernet0/2
 description TO_SW29_eth0/2
 no ip address
!         
interface Ethernet0/2.40
 encapsulation dot1Q 40
 ip address 192.168.40.1 255.255.255.0
 ip policy route-map V40
!         
interface Ethernet0/2.45
 encapsulation dot1Q 45
 ip address 172.20.45.33 255.255.255.240
!         
interface Ethernet0/2.50
 encapsulation dot1Q 50
 ip address 192.168.50.1 255.255.255.0
 ip policy route-map V50
...       
ip forward-protocol nd        
!         
no ip http server
no ip http secure-server
ip route 0.0.0.0 0.0.0.0 Ethernet0/2
!         
ip sla 1  
 icmp-echo 10.0.253.1
 threshold 1
 timeout 2
 frequency 5
ip sla schedule 1 life forever start-time now
ip sla 2  
 icmp-echo 10.0.61.1
 threshold 1
 timeout 4
 frequency 5
ip sla schedule 2 life forever start-time now

```
Также видно что для sub интерфесов применена команда "ip policy route-map V40"

### Тесты переклчения IP SLA:
Изначально мы видим что IP SLA в состоянии ОК

![](https://github.com/Ram170107/Otus_practice_ARR/blob/f500a053adfaea872af57cefb7b354f707e88988/labs/lab_5/IPSLA%20sum.png)

Далее если у нас выключается линк (порт eth0/3) на R25 в сторону R28, то IP SLA меняет свое состояние и маршрут перестраивается:

![](https://github.com/Ram170107/Otus_practice_ARR/blob/f500a053adfaea872af57cefb7b354f707e88988/labs/lab_5/R28_r25down.png)

Как мы видим ниже теперь пакеты до 10.0.252.2 идут через 10.0.61.1:
![](https://github.com/Ram170107/Otus_practice_ARR/blob/f500a053adfaea872af57cefb7b354f707e88988/labs/lab_5/%D0%9C%D0%B0%D1%80%D1%88%D1%80%D1%83%D1%82%20VPC31%20%D0%B4%D0%BE%20R26.png)
Включаем порт на R28 обратно, IP SLA восстанавливает состояния на OK:

![](https://github.com/Ram170107/Otus_practice_ARR/blob/f500a053adfaea872af57cefb7b354f707e88988/labs/lab_5/IPSLA%20sum.png)

Далее если мы выключаем линк (порт eth0/1) на R26 в сторону R28, то уже IP SLA 2 меняет свое состояние на Timeout и маршрут от VPC30 перестраивается:
![](https://github.com/Ram170107/Otus_practice_ARR/blob/f500a053adfaea872af57cefb7b354f707e88988/labs/lab_5/IP%20SLA2%20timeout.png)

#### Путем icmp запросов мы определяем что ответы о недоступности произвольного узла нам приходят по разным маршрутам:

```
*10.0.61.1 icmp_seq=41 ttl=254 time=0.718 ms (ICMP type:3, code:1, Destination host unreachable)
*10.0.61.1 icmp_seq=42 ttl=254 time=0.737 ms (ICMP type:3, code:1, Destination host unreachable)
*10.0.61.1 icmp_seq=43 ttl=254 time=0.687 ms (ICMP type:3, code:1, Destination host unreachable)
1.1.1.1 icmp_seq=44 timeout
1.1.1.1 icmp_seq=45 timeout
1.1.1.1 icmp_seq=46 timeout
1.1.1.1 icmp_seq=47 timeout
1.1.1.1 icmp_seq=48 timeout
*10.0.252.2 icmp_seq=49 ttl=253 time=0.880 ms (ICMP type:3, code:1, Destination host unreachable)
*10.0.252.2 icmp_seq=50 ttl=253 time=6.885 ms (ICMP type:3, code:1, Destination host unreachable)
*10.0.252.2 icmp_seq=51 ttl=253 time=0.905 ms (ICMP type:3, code:1, Destination host unreachable)
*10.0.252.2 icmp_seq=52 ttl=253 time=0.705 ms (ICMP type:3, code:1, Destination host unreachable)
*10.0.252.2 icmp_seq=53 ttl=253 time=0.854 ms (ICMP type:3, code:1, Destination host unreachable)
^C
VPC30> trace 10.0.252.2
trace to 10.0.252.2, 8 hops max, press Ctrl+C to stop
 1   192.168.40.1   0.328 ms  0.273 ms  0.259 ms
 2   10.0.253.1   0.479 ms  0.383 ms  0.378 ms
 3   *10.0.252.2   0.594 ms (ICMP type:3, code:3, Destination port unreachable)  *

VPC30> 

```

5 пакетов у нас потерялось при переключении маршрута

### Настройте для офиса Лабытнанги маршрут по-умолчанию.
В офисе Лабытнанги на маршрутизаторе R27 настроил маршрут по умолчанию:

```
R27#show ip route
...
...
S*    0.0.0.0/0 [1/0] via 10.0.251.1
      10.0.0.0/8 is variably subnetted, 3 subnets, 2 masks
C        10.0.251.0/30 is directly connected, Ethernet0/0
L        10.0.251.2/32 is directly connected, Ethernet0/0
C        10.52.0.27/32 is directly connected, Loopback0
      192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.1.0/24 is directly connected, Ethernet1/1.10
L        192.168.1.27/32 is directly connected, Ethernet1/1.10
R27#
```
Для того чтобы проверить корректность работы маршрутизации, настроил дополнительно на interface Eth 1/1 sub интерфейс и прописал ip адрес на нем:
```
R27#show running-config | section int       
mmi polling-interval 60
interface Loopback0
 ip address 10.52.0.27 255.255.255.255
interface Ethernet0/0
 description TO_R25_eth0/1
 ip address 10.0.251.2 255.255.255.252
interface Ethernet0/1
...
...
interface Ethernet1/1.10
 encapsulation dot1Q 10
 ip address 192.168.1.27 255.255.255.0

```
Для того чтобы на наши icmp запросы приходил ответ, настроили на R25 маршрут до сети 192.168.1.0/24 через ip адрес интерфейса eth0/0 маршрутизатора R27:
```
R25#sh ip route
...
      10.0.0.0/8 is variably subnetted, 9 subnets, 2 masks
C        10.0.231.0/30 is directly connected, Ethernet0/0
L        10.0.231.2/32 is directly connected, Ethernet0/0
C        10.0.251.0/30 is directly connected, Ethernet0/1
L        10.0.251.1/32 is directly connected, Ethernet0/1
C        10.0.252.0/30 is directly connected, Ethernet0/2
L        10.0.252.1/32 is directly connected, Ethernet0/2
C        10.0.253.0/30 is directly connected, Ethernet0/3
L        10.0.253.1/32 is directly connected, Ethernet0/3
C        10.52.0.25/32 is directly connected, Loopback0
S     192.168.1.0/24 [1/0] via 10.0.251.2
R25#
```

##### Результат команды ping от R27 до R25 и наоборот:

```
R27#ping 10.0.251.1 source 192.168.1.27
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.0.251.1, timeout is 2 seconds:
Packet sent with a source address of 192.168.1.27 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/5 ms
R27#ping 10.0.231.2 source 192.168.1.27
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.0.231.2, timeout is 2 seconds:
Packet sent with a source address of 192.168.1.27 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
R27#

```

```
R25#ping 192.168.1.27                  
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.27, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
R25#
```
##### Таким образом мы видим что из нашего локального ip адреса мы можем пинговать любой адрес на маршрутизаторе R25

### План работы и изменения зафиксированы в документации .
