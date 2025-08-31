## Настроить IS-IS офисе Триада
![](https://github.com/Ram170107/Otus_practice_ARR/blob/ac46155ea26409ecd94684ed0a846d6fb9ae19c4/labs/lab_7/isis.png)
> Область сети для настройки протокола ISIS

#### Настроите IS-IS в ISP Триада.
#### R23 и R25 находятся в зоне 2222.

```
R23#sh run | s isis
 ip router isis 2325
 isis network point-to-point 
 ip router isis 2325
 isis network point-to-point 
router isis 2325
 net 49.2222.0010.0052.0023.00
 is-type level-2-only
R23#sh isis ne
R23#sh isis neighbors 

Tag 2325:
System Id      Type Interface   IP Address      State Holdtime Circuit Id
R24            L2   Et0/2       10.0.232.2      UP    20       01
R25            L2   Et0/1       10.0.231.2      UP    28       00
R23#

```

```
R25#sh run | s isis
 ip router isis 2523
 isis network point-to-point 
 ip router isis 2523
 isis network point-to-point 
router isis 2523
 net 49.2222.0010.0052.0025.00
 is-type level-2-only
R25#sh isis ne
R25#sh isis neighbors 

Tag 2523:
System Id      Type Interface   IP Address      State Holdtime Circuit Id
R23            L2   Et0/0       10.0.231.1      UP    28       00
R26            L2   Et0/2       10.0.252.2      UP    28       01
R25#

```
#### Как мы видим из выгрузки с маршрутизаторов R23 и R25 на них настроен протокол ISIS.
- В строке net значение после первой точки указывает нам номер area - 49.**2222**.0010.0052.0025.00
#### R24 находится в зоне 24.
```
R24#show running-config | s isis
 ip router isis 24
 isis network point-to-point 
 ip router isis 24
 isis network point-to-point 
router isis 24
 net 49.0024.0010.0052.0024.00
 is-type level-2-only
R24#sh isis ne
R24#sh isis neighbors 

Tag 24:
System Id      Type Interface   IP Address      State Holdtime Circuit Id
R23            L2   Et0/2       10.0.232.1      UP    25       01
R26            L2   Et0/1       10.0.241.2      UP    27       00
R24#

```
- В строке net значение после первой точки указывает нам номер area - 49.00**24**.0010.0052.0024.00
#### R26 находится в зоне 26.
```
R26#sh run | s isis
 ip router isis 26
 isis network point-to-point 
 ip router isis 26
 isis network point-to-point 
router isis 26
 net 49.0026.0010.0052.0026.00
 is-type level-2-only
R26#sh isis ne
R26#sh isis neighbors 

Tag 26:
System Id      Type Interface   IP Address      State Holdtime Circuit Id
R24            L2   Et0/0       10.0.241.1      UP    21       00
R25            L2   Et0/2       10.0.252.1      UP    26       01
R26#

```
- Зесь мы также в строке net видим значение после первой точки указывающее на номер area - 49.00**26**.0010.0052.0026.00

### Также посмотрим маршруты прилетающие на маршрутизаторы по протоколу ISIS:

#### R23
```
R23#sh ip route is  
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
i L2     10.0.241.0/30 [115/20] via 10.0.232.2, 00:23:28, Ethernet0/2
i L2     10.0.252.0/30 [115/20] via 10.0.231.2, 00:23:28, Ethernet0/1
R23#

```
#### R25

``` 
R25#sh ip route isis
Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 11 subnets, 2 masks
i L2     10.0.232.0/30 [115/20] via 10.0.231.1, 00:24:10, Ethernet0/0
i L2     10.0.241.0/30 [115/20] via 10.0.252.2, 00:24:10, Ethernet0/2
R25#

```
#### R24

```
R24#sh ip route isis
Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 11 subnets, 2 masks
i L2     10.0.231.0/30 [115/20] via 10.0.232.1, 00:24:46, Ethernet0/2
i L2     10.0.252.0/30 [115/20] via 10.0.241.2, 00:24:46, Ethernet0/1
R24#

```

#### R26

```
R26#sh ip route isis
Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 11 subnets, 2 masks
i L2     10.0.231.0/30 [115/20] via 10.0.252.1, 00:23:04, Ethernet0/2
i L2     10.0.232.0/30 [115/20] via 10.0.241.1, 00:23:04, Ethernet0/0
R26#

```

#### Как мы можем видет от соседних маршрутизаторов прилетают маршруты по ISIS.
##### Сети настроены в режиме P2P, уровень L2. Ниже пример настройки типа сети на интерфейсе:

```
interface Ethernet0/2
 description TO_R26_eth0/2
 ip address 10.0.252.1 255.255.255.252
 ip router isis 2523
 isis network point-to-point 
!         
          
R25#
```
#### Посмотрим database таблицу где видны зоны, анонсируемые сети и метрики:
```
R25#sh isis database ver

Tag 2523:
IS-IS Level-2 Link State Database:
LSPID                 LSP Seq Num  LSP Checksum  LSP Holdtime      ATT/P/OL
R23.00-00             0x00000004   0x25DB        950               0/0/0
  Area Address: 49.2222
  NLPID:        0xCC 
  Hostname: R23
  IP Address:   10.0.232.1
  Metric: 10         IS R24.00
  Metric: 10         IS R25.00
  Metric: 10         IP 10.0.231.0 255.255.255.252
  Metric: 10         IP 10.0.232.0 255.255.255.252
R24.00-00             0x00000004   0x22F1        932               0/0/0
  Area Address: 49.0024
  NLPID:        0xCC 
  Hostname: R24
  IP Address:   10.0.232.2
  Metric: 10         IS R23.00
  Metric: 10         IS R26.00
  Metric: 10         IP 10.0.232.0 255.255.255.252
  Metric: 10         IP 10.0.241.0 255.255.255.252
R25.00-00           * 0x00000005   0x0FC4        944               0/0/0
  Area Address: 49.2222
  NLPID:        0xCC 
  Hostname: R25
  IP Address:   10.0.252.1
  Metric: 10         IS R23.00
  Metric: 10         IS R26.00
  Metric: 10         IP 10.0.231.0 255.255.255.252
  Metric: 10         IP 10.0.252.0 255.255.255.252
R26.00-00             0x00000005   0x974D        1101              0/0/0
  Area Address: 49.0026
  NLPID:        0xCC 
  Hostname: R26
  IP Address:   10.0.252.2
  Metric: 10         IS R24.00
  Metric: 10         IS R25.00
  Metric: 10         IP 10.0.241.0 255.255.255.252
  Metric: 10         IP 10.0.252.0 255.255.255.252
R25#

```

#### Проверим связность между R23 и другими маршрутизаторами в сети ISIS:

```
R23#ping 10.0.241.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.0.241.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
R23#

```
```
R23#ping 10.0.252.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.0.252.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
R23#

```
```
R23#ping 10.0.252.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.0.252.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
R23#

```
