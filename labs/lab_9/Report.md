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
##### 3. Настроите eBGP между Ламас и Триада.

##### 4. Настроите eBGP между офисом С.-Петербург и провайдером Триада.
##### 5. Организуете IP доступность между пограничным роутерами офисами Москва и С.-Петербург.
