##                                 OSPF
#### Цель:
- Настроить OSPF офисе Москва
- Разделить сеть на зоны
- Настроить фильтрацию между зонами

#### Общая схема


### Маршрутизаторы R14-R15 находятся в зоне 0 - backbone.
```
R14#
...
interface Loopback0
 ip address 10.1.0.14 255.255.255.255
!         
interface Ethernet0/0
 description TO_R12_eth0/2
 ip address 10.0.140.1 255.255.255.252
 ip ospf 14 area 0
!         
interface Ethernet0/1
 description TO_R13_eth0/3
 ip address 10.0.141.1 255.255.255.252
 ip ospf 14 area 0
!         
interface Ethernet0/2
 description TO_Kitorn_R22_eth0/0
 ip address 10.0.220.2 255.255.255.252
!         
interface Ethernet0/3
 description TO_R19_eth0/0
 ip address 10.0.143.1 255.255.255.252
 ip ospf 14 area 101
!         
router ospf 14
 router-id 1.1.1.14
 area 101 stub no-summary
 network 10.0.140.0 0.0.0.3 area 0
 network 10.0.141.0 0.0.0.3 area 0
 network 10.0.143.0 0.0.0.3 area 0
 network 10.0.220.0 0.0.0.3 area 0
 network 10.1.0.14 0.0.0.0 area 0
 network 0.0.0.0 255.255.255.255 area 0
 default-information originate
!         
ip forward-protocol nd     
!         
no ip http server
no ip http secure-server
ip route 0.0.0.0 0.0.0.0 10.0.220.1
!                
control-plane
!         
!         
      
R14#

```

```
R15#
...
interface Loopback0
 ip address 10.1.0.15 255.255.255.255
!         
interface Ethernet0/0
 description TO_R13_eth0/2
 ip address 10.0.150.1 255.255.255.252
!         
interface Ethernet0/1
 description TO_R12_eth0/3
 ip address 10.0.151.1 255.255.255.252
!         
interface Ethernet0/2
 description TO_Lamas_R21_eth0/0
 ip address 10.0.210.2 255.255.255.252
!         
interface Ethernet0/3
 description TO_R20_eth0/0
 ip address 10.0.153.1 255.255.255.252
 ip ospf 15 area 102
!         
router ospf 15
 router-id 1.1.1.15
 network 10.0.150.0 0.0.0.3 area 0
 network 10.0.151.0 0.0.0.3 area 0
 network 10.0.153.0 0.0.0.3 area 0
 network 10.0.210.0 0.0.0.3 area 0
 network 10.1.0.15 0.0.0.0 area 0
!         
R15#
```
#### Вывод команд "show ip route ospf"
```
R14#sh ip route ospf
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is 10.0.220.1 to network 0.0.0.0

      10.0.0.0/8 is variably subnetted, 24 subnets, 2 masks
O        10.0.150.0/30 [110/20] via 10.0.141.2, 00:33:44, Ethernet0/1
O        10.0.151.0/30 [110/20] via 10.0.140.2, 00:33:34, Ethernet0/0
O IA     10.0.153.0/30 [110/30] via 10.0.141.2, 00:33:34, Ethernet0/1
                       [110/30] via 10.0.140.2, 00:33:34, Ethernet0/0
O        10.0.210.0/30 [110/30] via 10.0.141.2, 00:33:34, Ethernet0/1
                       [110/30] via 10.0.140.2, 00:33:34, Ethernet0/0
O        10.1.0.15/32 [110/21] via 10.0.141.2, 00:33:34, Ethernet0/1
                      [110/21] via 10.0.140.2, 00:33:34, Ethernet0/0
O IA     10.2.0.12/32 [110/11] via 10.0.140.2, 00:33:34, Ethernet0/0
O IA     10.2.0.13/32 [110/11] via 10.0.141.2, 00:33:44, Ethernet0/1
O IA     10.3.0.4/32 [110/21] via 10.0.141.2, 00:33:34, Ethernet0/1
                     [110/21] via 10.0.140.2, 00:33:33, Ethernet0/0
O IA     10.3.0.5/32 [110/21] via 10.0.141.2, 00:33:44, Ethernet0/1
                     [110/21] via 10.0.140.2, 00:33:34, Ethernet0/0
O        10.5.0.19/32 [110/11] via 10.0.143.2, 00:33:44, Ethernet0/3
O IA     10.5.0.20/32 [110/31] via 10.0.141.2, 00:33:34, Ethernet0/1
                      [110/31] via 10.0.140.2, 00:33:34, Ethernet0/0
O IA     10.10.120.0/30 [110/20] via 10.0.140.2, 00:33:34, Ethernet0/0
O IA     10.10.121.0/30 [110/20] via 10.0.140.2, 00:33:34, Ethernet0/0
O IA     10.10.130.0/30 [110/20] via 10.0.141.2, 00:33:44, Ethernet0/1
O IA     10.10.131.0/30 [110/20] via 10.0.141.2, 00:33:44, Ethernet0/1
      172.10.0.0/24 is subnetted, 1 subnets
O        172.10.19.0 [110/20] via 10.0.143.2, 00:33:44, Ethernet0/3
R14#

```

```
R15#sh ip route ospf
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is 10.0.151.2 to network 0.0.0.0

O*E2  0.0.0.0/0 [110/10] via 10.0.151.2, 00:32:47, Ethernet0/1
                [110/10] via 10.0.150.2, 00:32:47, Ethernet0/0
      10.0.0.0/8 is variably subnetted, 24 subnets, 2 masks
O        10.0.140.0/30 [110/20] via 10.0.151.2, 00:32:47, Ethernet0/1
O        10.0.141.0/30 [110/20] via 10.0.150.2, 00:32:57, Ethernet0/0
O IA     10.0.143.0/30 [110/30] via 10.0.151.2, 00:32:47, Ethernet0/1
                       [110/30] via 10.0.150.2, 00:32:47, Ethernet0/0
O        10.0.220.0/30 [110/30] via 10.0.151.2, 00:32:47, Ethernet0/1
                       [110/30] via 10.0.150.2, 00:32:47, Ethernet0/0
O        10.1.0.14/32 [110/21] via 10.0.151.2, 00:32:47, Ethernet0/1
                      [110/21] via 10.0.150.2, 00:32:47, Ethernet0/0
O IA     10.2.0.12/32 [110/11] via 10.0.151.2, 00:32:47, Ethernet0/1
O IA     10.2.0.13/32 [110/11] via 10.0.150.2, 00:32:57, Ethernet0/0
O IA     10.3.0.4/32 [110/21] via 10.0.151.2, 00:32:46, Ethernet0/1
                     [110/21] via 10.0.150.2, 00:32:47, Ethernet0/0
O IA     10.3.0.5/32 [110/21] via 10.0.151.2, 00:32:47, Ethernet0/1
                     [110/21] via 10.0.150.2, 00:32:57, Ethernet0/0
O IA     10.5.0.19/32 [110/31] via 10.0.151.2, 00:32:47, Ethernet0/1
                      [110/31] via 10.0.150.2, 00:32:47, Ethernet0/0
O        10.5.0.20/32 [110/11] via 10.0.153.2, 00:32:57, Ethernet0/3
O IA     10.10.120.0/30 [110/20] via 10.0.151.2, 00:32:47, Ethernet0/1
O IA     10.10.121.0/30 [110/20] via 10.0.151.2, 00:32:47, Ethernet0/1
O IA     10.10.130.0/30 [110/20] via 10.0.150.2, 00:32:57, Ethernet0/0
O IA     10.10.131.0/30 [110/20] via 10.0.150.2, 00:32:57, Ethernet0/0
      172.10.0.0/24 is subnetted, 1 subnets
O IA     172.10.19.0 [110/40] via 10.0.151.2, 00:32:47, Ethernet0/1
                     [110/40] via 10.0.150.2, 00:32:47, Ethernet0/0
R15#

```
#### Смотрим соседей у R14 и R15:
```
R14#show ip ospf neighbor 

Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.113         1   FULL/DR         00:00:33    10.0.141.2      Ethernet0/1
2.2.2.112         1   FULL/DR         00:00:30    10.0.140.2      Ethernet0/0
1.1.1.119         1   FULL/DR         00:00:39    10.0.143.2      Ethernet0/3
R14#

```
```
R15#show ip ospf neighbor 

Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.112         1   FULL/DR         00:00:36    10.0.151.2      Ethernet0/1
2.2.2.113         1   FULL/DR         00:00:34    10.0.150.2      Ethernet0/0
2.2.2.20          1   FULL/DR         00:00:38    10.0.153.2      Ethernet0/3
R15#

```
### Маршрутизаторы R12-R13 находятся в зоне 10. Дополнительно к маршрутам должны получать маршрут по умолчанию.
```
R12#
...
interface Loopback0
 ip address 10.2.0.12 255.255.255.255
!         
interface Ethernet0/0
 description TO_SW4_eth1/0
 ip address 10.10.120.1 255.255.255.252
 ip ospf 2 area 20
!         
interface Ethernet0/1
 description TO_SW5_eth1/1
 ip address 10.10.121.1 255.255.255.252
 ip ospf 2 area 20
!         
interface Ethernet0/2
 description TO_R14_eth0/0_Area_0
 ip address 10.0.140.2 255.255.255.252
 ip ospf 2 area 0
!         
interface Ethernet0/3
 description TO_R15_eth0/1_Area_0
 ip address 10.0.151.2 255.255.255.252
 ip ospf 2 area 0
!                
router ospf 2
 router-id 2.2.2.112
 network 10.0.120.0 0.0.0.3 area 10
 network 10.0.121.0 0.0.0.3 area 10
 network 10.0.140.0 0.0.0.3 area 10
 network 10.0.151.0 0.0.0.3 area 10
 network 10.2.0.12 0.0.0.0 area 10
R12#
```

```
R13#
...
interface Loopback0
 ip address 10.2.0.13 255.255.255.255
!         
interface Ethernet0/0
 description TO_SW5_eth1/0
 ip address 10.10.130.1 255.255.255.252
 ip ospf 13 area 20
!         
interface Ethernet0/1
 description TO_SW4_eth1/1
 ip address 10.10.131.1 255.255.255.252
 ip ospf 13 area 20
!         
interface Ethernet0/2
 description TO_R15_eth0/0
 ip address 10.0.150.2 255.255.255.252
 ip ospf 13 area 0
!         
interface Ethernet0/3
 description TO_R14_eth0/1
 ip address 10.0.141.2 255.255.255.252
 ip ospf 13 area 0
!         
router ospf 13
 router-id 2.2.2.113
 network 10.2.0.13 0.0.0.0 area 10
 network 10.10.130.0 0.0.0.3 area 10
 network 10.10.131.0 0.0.0.3 area 10
 network 10.10.141.0 0.0.0.3 area 10
 network 10.10.150.0 0.0.0.3 area 0
R13#
```
Видим что у R12 и R13 имеются 2 порта направленных в сторону зоны 0 (backbone)
Также на SW4 и SW5 для нормальной связности настроил OSPF в 20 зоне куда и смотрят ещё 2 порта у маршрутизаторов R12 и R13
#### Маршруты OSPF в R12 и R13:
```
R12#show ip route ospf
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is 10.0.140.1 to network 0.0.0.0

O*E2  0.0.0.0/0 [110/10] via 10.0.140.1, 00:42:29, Ethernet0/2
      10.0.0.0/8 is variably subnetted, 24 subnets, 2 masks
O        10.0.141.0/30 [110/20] via 10.0.140.1, 00:42:29, Ethernet0/2
O IA     10.0.143.0/30 [110/20] via 10.0.140.1, 00:42:29, Ethernet0/2
O        10.0.150.0/30 [110/20] via 10.0.151.1, 00:42:19, Ethernet0/3
O IA     10.0.153.0/30 [110/20] via 10.0.151.1, 00:42:19, Ethernet0/3
O        10.0.210.0/30 [110/20] via 10.0.151.1, 00:42:19, Ethernet0/3
O        10.0.220.0/30 [110/20] via 10.0.140.1, 00:42:29, Ethernet0/2
O        10.1.0.14/32 [110/11] via 10.0.140.1, 00:42:29, Ethernet0/2
O        10.1.0.15/32 [110/11] via 10.0.151.1, 00:42:19, Ethernet0/3
O IA     10.2.0.13/32 [110/21] via 10.0.151.1, 00:42:19, Ethernet0/3
                      [110/21] via 10.0.140.1, 00:42:29, Ethernet0/2
O        10.3.0.4/32 [110/11] via 10.10.120.2, 00:42:19, Ethernet0/0
O        10.3.0.5/32 [110/11] via 10.10.121.2, 00:42:29, Ethernet0/1
O IA     10.5.0.19/32 [110/21] via 10.0.140.1, 00:42:29, Ethernet0/2
O IA     10.5.0.20/32 [110/21] via 10.0.151.1, 00:42:19, Ethernet0/3
O        10.10.130.0/30 [110/20] via 10.10.121.2, 00:42:29, Ethernet0/1
O        10.10.131.0/30 [110/20] via 10.10.120.2, 00:42:19, Ethernet0/0
      172.10.0.0/24 is subnetted, 1 subnets
O IA     172.10.19.0 [110/30] via 10.0.140.1, 00:42:29, Ethernet0/2
R12#

```

```
R13#show ip route ospf
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is 10.0.141.1 to network 0.0.0.0

O*E2  0.0.0.0/0 [110/10] via 10.0.141.1, 00:41:54, Ethernet0/3
      10.0.0.0/8 is variably subnetted, 24 subnets, 2 masks
O        10.0.140.0/30 [110/20] via 10.0.141.1, 00:41:44, Ethernet0/3
O IA     10.0.143.0/30 [110/20] via 10.0.141.1, 00:41:54, Ethernet0/3
O        10.0.151.0/30 [110/20] via 10.0.150.1, 00:41:44, Ethernet0/2
O IA     10.0.153.0/30 [110/20] via 10.0.150.1, 00:41:54, Ethernet0/2
O        10.0.210.0/30 [110/20] via 10.0.150.1, 00:41:54, Ethernet0/2
O        10.0.220.0/30 [110/20] via 10.0.141.1, 00:41:54, Ethernet0/3
O        10.1.0.14/32 [110/11] via 10.0.141.1, 00:41:54, Ethernet0/3
O        10.1.0.15/32 [110/11] via 10.0.150.1, 00:41:54, Ethernet0/2
O IA     10.2.0.12/32 [110/21] via 10.0.150.1, 00:41:44, Ethernet0/2
                      [110/21] via 10.0.141.1, 00:41:44, Ethernet0/3
O        10.3.0.4/32 [110/11] via 10.10.131.2, 00:41:44, Ethernet0/1
O        10.3.0.5/32 [110/11] via 10.10.130.2, 00:41:54, Ethernet0/0
O IA     10.5.0.19/32 [110/21] via 10.0.141.1, 00:41:54, Ethernet0/3
O IA     10.5.0.20/32 [110/21] via 10.0.150.1, 00:41:54, Ethernet0/2
O        10.10.120.0/30 [110/20] via 10.10.131.2, 00:41:44, Ethernet0/1
O        10.10.121.0/30 [110/20] via 10.10.130.2, 00:41:54, Ethernet0/0
      172.10.0.0/24 is subnetted, 1 subnets
O IA     172.10.19.0 [110/30] via 10.0.141.1, 00:41:54, Ethernet0/3
R13#

```
#### Соседи OSPF у маршрутизаторов R12 и R13:
```
R12#show ip ospf neighbor 

Neighbor ID     Pri   State           Dead Time   Address         Interface
1.1.1.15          1   FULL/BDR        00:00:36    10.0.151.1      Ethernet0/3
1.1.1.14          1   FULL/BDR        00:00:35    10.0.140.1      Ethernet0/2
3.3.3.5           1   FULL/DR         00:00:34    10.10.121.2     Ethernet0/1
3.3.3.4           1   FULL/DR         00:00:35    10.10.120.2     Ethernet0/0
R12#

```

```
R13#show ip ospf neighbor 

Neighbor ID     Pri   State           Dead Time   Address         Interface
1.1.1.14          1   FULL/BDR        00:00:35    10.0.141.1      Ethernet0/3
1.1.1.15          1   FULL/BDR        00:00:36    10.0.150.1      Ethernet0/2
3.3.3.4           1   FULL/DR         00:00:31    10.10.131.2     Ethernet0/1
3.3.3.5           1   FULL/DR         00:00:32    10.10.130.2     Ethernet0/0
R13#

```
### Маршрутизатор R19 находится в зоне 101 и получает только маршрут по умолчанию.

### Маршрутизатор R20 находится в зоне 102 и получает все маршруты, кроме маршрутов до сетей зоны 101.
