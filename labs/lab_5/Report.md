### Настроить политику маршрутизации в офисе Чокурдах
### Распределить трафик между 2 линками

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

![](https://github.com/Ram170107/Otus_practice_ARR/blob/ba40f15173f260528d846c620c29528649b90c39/labs/lab_5/%D0%9F%D0%B8%D0%BD%D0%B3%20PC31%20%D0%B4%D0%BE%20%D1%88%D0%BB%D1%8E%D0%B7%D0%B0.png)

> Пинг от PC31 до шлюза

![](https://github.com/Ram170107/Otus_practice_ARR/blob/ba40f15173f260528d846c620c29528649b90c39/labs/lab_5/%D0%A2%D0%B5%D1%81%D1%82%20%D0%BF%D0%B8%D0%BD%D0%B3%20%D0%B4%D0%BE%20%D0%9F%D0%9A%20%D0%BE%D1%84%D0%B8%D1%81%D0%BE%D0%B2.png)

> Пинг отшлюза до PC30 и PC31

### Распределите трафик между двумя линками с провайдером.
### Настроите отслеживание линка через технологию IP SLA.(только для IPv4)
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
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is not set

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
