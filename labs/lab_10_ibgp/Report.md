# iBGP

### Цель:
- Настроить iBGP в офисе Москва
- Настроить iBGP в сети провайдера Триада
- Организовать полную IP связанность всех сетей
  
![](https://github.com/Ram170107/Otus_practice_ARR/blob/f76ce35849a8444a3f49b15301e816b8bb7edf97/labs/lab_10_ibgp/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%BA%D0%B0%20IBGP.png)

### 1. Настроите iBGP в офисе Москва между маршрутизаторами R14 и R15.

#### Для повышения доступности IBGP обычно устанавливается между Loopback-интерфейсами
Настроим iBGP на R14 и R15:

```
R14#sh run | s router
...
router bgp 1001
 bgp router-id 1.1.1.14
 bgp log-neighbor-changes
 bgp default local-preference 500
 network 10.0.220.0 mask 255.255.255.252
 network 10.1.0.14 mask 255.255.255.255
 redistribute ospf 14 metric 20
 neighbor 10.0.220.1 remote-as 101
 neighbor 10.1.0.15 remote-as 1001
 neighbor 10.1.0.15 update-source Loopback0
 neighbor 10.1.0.15 next-hop-self
R14#

```

```
R15#sh run  | s router
router ospf 15
...
router bgp 1001
 bgp router-id 1.1.1.15
 bgp log-neighbor-changes
 bgp default local-preference 1000
 network 10.0.210.0 mask 255.255.255.252
 redistribute ospf 15 metric 10
 neighbor 10.0.210.1 remote-as 301
 neighbor 10.1.0.14 remote-as 1001
 neighbor 10.1.0.14 update-source Loopback0
 neighbor 10.1.0.14 next-hop-self
R15#

```
##### Посмотрим iBGP соседей у R14 и R15:
![](https://github.com/Ram170107/Otus_practice_ARR/blob/f76ce35849a8444a3f49b15301e816b8bb7edf97/labs/lab_10_ibgp/BGP%20neib%20R14.png)

![](https://github.com/Ram170107/Otus_practice_ARR/blob/f76ce35849a8444a3f49b15301e816b8bb7edf97/labs/lab_10_ibgp/BGP%20neib%20R15.png)

##### Как мы видим здесь у нас internal link в состоянии Estableshed

### Настроите iBGP в провайдере Триада, с использованием RR.


### Настройте офиса Москва так, чтобы приоритетным провайдером стал Ламас.
##### На R14 и R15 для выбора приоритетного маршрута настроил параметр Local preference. 
На R14 - 1000 (приоритетный маршрут) На R15 - 500
От оператора к нам приходит только маршрут по умолчанию.

```
R14#sh ip bgp
BGP table version is 24, local router ID is 1.1.1.14
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 r>i 0.0.0.0          10.1.0.15                0   1000      0 301 i
 r                    10.0.220.1                             0 101 i
 * i 10.0.140.0/30    10.1.0.15               10   1000      0 ?
 *>                   0.0.0.0                  0         32768 ?
 * i 10.0.141.0/30    10.1.0.15               10   1000      0 ?
..
```

```
R15#sh ip bgp 
BGP table version is 24, local router ID is 1.1.1.15
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  0.0.0.0          10.0.210.1                             0 301 i
 * i 10.0.140.0/30    10.1.0.14                0    500      0 ?
 *>                   10.0.151.2              10         32768 ?
 * i 10.0.141.0/30    10.1.0.14                0    500      0 ?
 *>                   10.0.150.2              10         32768 ?
..

```
Видим что маршрут выбирается через Ламас

Проверим связность и путь прохождения пакетов от PC до сети Триады:

```
VPC1> ping 10.0.222.2

84 bytes from 10.0.222.2 icmp_seq=1 ttl=251 time=1.239 ms
84 bytes from 10.0.222.2 icmp_seq=2 ttl=251 time=1.448 ms
84 bytes from 10.0.222.2 icmp_seq=3 ttl=251 time=1.522 ms
84 bytes from 10.0.222.2 icmp_seq=4 ttl=251 time=1.429 ms
84 bytes from 10.0.222.2 icmp_seq=5 ttl=251 time=1.420 ms

VPC1> tracer 10.0.222.2
trace to 10.0.222.2, 8 hops max, press Ctrl+C to stop
 1   192.168.10.2   0.644 ms  0.376 ms  0.357 ms
 2   10.10.120.1   0.580 ms  0.502 ms  0.507 ms
 3   10.0.151.1   0.715 ms  0.625 ms  0.607 ms
 4   10.0.210.1   0.925 ms  0.689 ms  0.745 ms
 5   10.0.221.1   0.841 ms  0.823 ms  0.862 ms
 6   *10.0.222.2   0.958 ms (ICMP type:3, code:3, Destination port unreachable)  *

VPC1> 

```

### Настройте офиса С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно.
В С.-Петербурге на R18 мы добавим команду  maximum-paths 2 - обозначив что у нас максимум 2 маршрута.
С помощью команды можно настроить балансировку нагрузки, распределив между несколькими линками трафик, направленный в одну сеть. 
При этом должны выполняться определённые условия:
В таблице BGP для сети должно быть не менее двух маршрутов.
Оба маршрута должны идти через одного провайдера.
Параметры Weight, Local Preference, AS-Path, Origin, MED и метрика IGP должны совпадать.
Параметр Next Hop должен быть разным для двух маршрутов.

```
R18#sh run | s router
router bgp 2042
 bgp log-neighbor-changes
 network 10.0.63.0 mask 255.255.255.252
 network 10.0.243.0 mask 255.255.255.252
 neighbor 10.0.63.1 remote-as 520
 neighbor 10.0.243.1 remote-as 520
 maximum-paths 2
R18#

```

#### Что и мы увидим ниже:

```
R18#sh ip bgp
BGP table version is 5, local router ID is 10.42.0.18
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 rm  0.0.0.0          10.0.243.1                             0 520 i
 r>                   10.0.63.1                              0 520 i
 *>  10.0.63.0/30     0.0.0.0                  0         32768 i
 *>  10.0.243.0/30    0.0.0.0                  0         32768 i

```

### Все сети в лабораторной работе должны иметь IP связность.
### План работы и изменения зафиксированы в документации.
