# iBGP

### Цель:
- Настроить iBGP в офисе Москва
- Настроить iBGP в сети провайдера Триада
- Организовать полную IP связанность всех сетей

### Настроите iBGP в офисом Москва между маршрутизаторами R14 и R15.
Настроим iBGP на R14 и R15:
#### Для повышения доступностиIBGP обычно устанавливается между Loopback-интерфейсами

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

### Настроите iBGP в провайдере Триада, с использованием RR.
### Настройте офиса Москва так, чтобы приоритетным провайдером стал Ламас.
### Настройте офиса С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно.
### Все сети в лабораторной работе должны иметь IP связность.
### План работы и изменения зафиксированы в документации.
