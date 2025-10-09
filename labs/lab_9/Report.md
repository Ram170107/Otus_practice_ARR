## Настроить BGP между автономными системами
#### Организовать доступность между офисами Москва и С.-Петербург

![](https://github.com/Ram170107/Otus_practice_ARR/blob/865fa89d441f52cf2804c38e4a12107a701355c6/labs/lab_9/%D0%A1%D1%85%D0%B5%D0%BC%D0%B0%20BGP.png)
Общая схема организации BGP

##### 1. Настроите eBGP между офисом Москва и двумя провайдерами - Киторн и Ламас.
- Настройка BGP на R14 и R15 в Москве

```
R14#sh run  | s bgp
router bgp 1001
 bgp log-neighbor-changes
 network 10.0.220.0 mask 255.255.255.252
 neighbor 10.0.220.1 remote-as 101
R14#
```

```
R15#sh run | s bgp
router bgp 1001
 bgp log-neighbor-changes
 network 10.0.210.0 mask 255.255.255.252
 neighbor 10.0.210.1 remote-as 301
R15#
```

```
R14#sh bgp nei
BGP neighbor is 10.0.220.1,  remote AS 101, external link
  BGP version 4, remote router ID 10.1.1.1
  BGP state = Established, up for 01:36:09
  Last read 00:00:14, last write 00:00:21, hold time is 180, keepalive interval is 60 seconds
  Neighbor sessions:
    1 active, is not multisession capable (disabled)
  Neighbor capabilities:
    Route refresh: advertised and received(new)
    Four-octets ASN Capability: advertised and received
    Address family IPv4 Unicast: advertised and received
    Enhanced Refresh Capability: advertised and received
    Multisession Capability: 
    Stateful switchover support enabled: NO for session 1
```
- как мы видим external link т.к. разные AS т.е. у нас eBGP

##### 2. Настроите eBGP между провайдерами Киторн и Ламас.

- Настройка BGP на R22 Киторн

```
  R22#sh run | s router
router bgp 101
 bgp log-neighbor-changes
 network 10.0.220.0 mask 255.255.255.252
 network 10.0.221.0 mask 255.255.255.252
 network 10.0.222.0 mask 255.255.255.252
 neighbor 10.0.220.2 remote-as 1001
 neighbor 10.0.221.2 remote-as 301
 neighbor 10.0.222.2 remote-as 520
 default-information originate
R22#
```
- Настройка BGP на R21 Ламас

```
R21#sh run | s bgp
router bgp 301
 bgp log-neighbor-changes
 network 10.0.210.0 mask 255.255.255.252
 network 10.0.212.0 mask 255.255.255.252
 network 10.0.221.0 mask 255.255.255.252
 neighbor 10.0.210.2 remote-as 1001
 neighbor 10.0.212.2 remote-as 520
 neighbor 10.0.221.1 remote-as 101
R21#
```
- Проверим связность между R15 и R22
  
```
R15#ping 10.0.222.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.0.222.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
R15#

```
- Проверим связность между R14 и R21

```
R14#ping 10.0.212.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.0.212.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
R14#

```

```
BGP neighbor is 10.0.221.2,  remote AS 301, external link
  BGP version 4, remote router ID 10.1.1.2
  BGP state = Established, up for 01:37:28
  Last read 00:00:46, last write 00:00:46, hold time is 180, keepalive interval is 60 seconds
  Neighbor sessions:
          
R22#
```
- как мы видим external link т.к. разные AS т.е. у нас eBGP

##### 3. Настроите eBGP между Ламас и Триада.
- Настройка BGP на R21 Ламас

```
R21#sh run | s bgp
router bgp 301
 bgp log-neighbor-changes
 network 10.0.210.0 mask 255.255.255.252
 network 10.0.212.0 mask 255.255.255.252
 network 10.0.221.0 mask 255.255.255.252
 neighbor 10.0.210.2 remote-as 1001
 neighbor 10.0.212.2 remote-as 520
 neighbor 10.0.221.1 remote-as 101
R21#
```
- Настройка BGP на R24 Триада
```
R24#sh run | s router
 ip router isis 24
 ip router isis 24
router isis 24
 net 49.0024.0010.0052.0024.00
 is-type level-2-only
 redistribute bgp 520
router bgp 520
 bgp log-neighbor-changes
 network 10.0.212.0 mask 255.255.255.252
 network 10.0.232.0 mask 255.255.255.252
 network 10.0.241.0 mask 255.255.255.252
 network 10.0.243.0 mask 255.255.255.252
 redistribute isis 24 level-2 metric 10
 neighbor 10.0.212.1 remote-as 301
 neighbor 10.0.243.2 remote-as 2042
R24#

R24#sh ip bgp nei
BGP neighbor is 10.0.212.1,  remote AS 301, external link
  BGP version 4, remote router ID 10.1.1.2
  BGP state = Established, up for 01:31:51
  Last read 00:00:44, last write 00:00:20, hold time is 180, keepalive interval is 60 seconds

```
- В настройках R24 BGP и ISIS в Триаде мы можем видеть строку с настройкой редистрибьюции между протоколами маршрутизации

- Проверим связность:

```
R21#ping 10.0.243.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.0.243.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
R21#

```
##### 4. Настроите eBGP между офисом С.-Петербург и провайдером Триада.
- Настройка BGP на R18 С. Петербург:
```
R18#sh run | s router
router bgp 2042
 bgp log-neighbor-changes
 network 10.0.63.0 mask 255.255.255.252
 network 10.0.243.0 mask 255.255.255.252
 neighbor 10.0.63.1 remote-as 520
 neighbor 10.0.243.1 remote-as 520
R18#

```

```
R18#sh ip bgp neighbors 
BGP neighbor is 10.0.63.1,  remote AS 520, external link
  BGP version 4, remote router ID 10.52.0.26
  BGP state = Established, up for 01:20:40
  Last read 00:00:05, last write 00:00:28, hold time is 180, keepalive interval is 60 seconds

BGP neighbor is 10.0.243.1,  remote AS 520, external link
  BGP version 4, remote router ID 10.52.0.24
  BGP state = Established, up for 01:25:02
  Last read 00:00:02, last write 00:00:48, hold time is 180, keepalive interval is 60 seconds
  Neighbor sessions:
    1 active, is not multisession capable (disabled)
  Neighbor capabilities:
          
R18#
```
- Проверим связность:

```
R18#ping 10.0.232.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.0.232.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
R18#             
R18#ping 10.0.252.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.0.252.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
R18#
```

##### 5. Организуете IP доступность между пограничным роутерами офисами Москва и С.-Петербург.
