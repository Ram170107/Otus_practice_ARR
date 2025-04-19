# Основные цели:
### Часть 1. Построение сети и настройка основынх параметров
### Часть 2. Настройка и проверка двух серверов DHCPv4 на R1
### Часть 3. Настройка и проверка DHCP-ретранслятора на R2
______

### Часть 1. 
#### Создайте сеть и настройте основные параметры устройства
![](https://github.com/Ram170107/Otus_practice_ARR/blob/c7f6f24d02d96edf9376a2eebf2c55240de46d29/labs/lab_3/%D0%A1%D1%85%D0%B5%D0%BC%D0%B0_ipv4.png)
> Схема из лабороторной работы eve.ng

#### Шаг 1:
Подсеть сети 192.168.1.0/24 должна соответствовать следующим требованиям:
- а. Подсеть A поддерживающая 58 хостов (клиентская VLAN на R1)
```
Мною была выбрана подсеть 192.168.1.0 с маской 255.255.255.192 (/26)
что имеет в своём диапазоне 62 ip адреса, что подходит под требование 58 хостов
```
- b. Подсеть B поддерживающая 28 хостов (управляющая VLAN на R1)
```
Под данные требования я выбрал подсеть 192.168.1.64 с маской 255.255.255.224 (/27)
что в себя включает 30 ip адресов, что подходить под требование в 28 хостов
```
- с. Подсеть C поддерживающая 12 хостов (Клиентская сеть на уровне R2)

```
Мною была выбрана подсеть 192.168.1.96 255.255.255.240 (/28) что в себя включает
 14 ip адресов, что подходить под требование 12 хостов
```
![](https://github.com/Ram170107/Otus_practice_ARR/blob/5da15b062c3d270ee7368ef24f9959257e4ee4b5/labs/lab_3/Addres%20table.png)
![](https://github.com/Ram170107/Otus_practice_ARR/blob/5da15b062c3d270ee7368ef24f9959257e4ee4b5/labs/lab_3/VLAN%20table.png)

#### Шаг 3. Настройте основные параметры каждого маршрутизатора

- Параметры настройены. Смотреть файлы конфига маршрутизаторов:
  https://github.com/Ram170107/Otus_practice_ARR/blob/88045954bd2759fe573e6e709b5026c58fe2226f/labs/lab_3/R1_conf
  https://github.com/Ram170107/Otus_practice_ARR/blob/88045954bd2759fe573e6e709b5026c58fe2226f/labs/lab_3/R2_conf
  
#### Шаг 4. Настройте маршрутизацию между VLAN на R1
- Параметры настройены:
  
```
  
  R1#
interface Ethernet0/1.100
 description Clients
 encapsulation dot1Q 100
 ip address 192.168.1.1 255.255.255.192
!         
interface Ethernet0/1.200
 description Management
 encapsulation dot1Q 200
 ip address 192.168.1.65 255.255.255.224
```


 Смотреть файлы конфига маршрутизаторов:
  https://github.com/Ram170107/Otus_practice_ARR/blob/88045954bd2759fe573e6e709b5026c58fe2226f/labs/lab_3/R1_conf
  https://github.com/Ram170107/Otus_practice_ARR/blob/88045954bd2759fe573e6e709b5026c58fe2226f/labs/lab_3/R2_conf
  
#### Шаг 5.  Настройте Eth0/0 на R2, затем Eth0/1 и статическую маршрутизацию для обоих маршрутизаторов:
```

R1#
interface Ethernet0/0
 ip address 10.0.0.1 255.255.255.252
!         
interface Ethernet0/1
 no ip address
R1#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is 10.0.0.2 to network 0.0.0.0

S*    0.0.0.0/0 [1/0] via 10.0.0.2
      10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C        10.0.0.0/30 is directly connected, Ethernet0/0
L        10.0.0.1/32 is directly connected, Ethernet0/0
      192.168.1.0/24 is variably subnetted, 4 subnets, 3 masks
C        192.168.1.0/26 is directly connected, Ethernet0/1.100
L        192.168.1.1/32 is directly connected, Ethernet0/1.100
C        192.168.1.64/27 is directly connected, Ethernet0/1.200
L        192.168.1.65/32 is directly connected, Ethernet0/1.200

```

```

R2#
interface Ethernet0/0
 ip address 10.0.0.2 255.255.255.252
!         
interface Ethernet0/1
 ip address 192.168.1.97 255.255.255.240
 ip helper-address 10.0.0.1
R2#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is 10.0.0.1 to network 0.0.0.0

S*    0.0.0.0/0 [1/0] via 10.0.0.1
      10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C        10.0.0.0/30 is directly connected, Ethernet0/0
L        10.0.0.2/32 is directly connected, Ethernet0/0
      192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.1.96/28 is directly connected, Ethernet0/1
L        192.168.1.97/32 is directly connected, Ethernet0/1

```
##### Убедитесь что статическая маршрутизация работает, отправиви запрос на адрес R2 Eth0/0
```
R1#ping 10.0.0.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.0.0.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
R1#


```

#### Шаг 6. Настройте основные параметры для каждого коммутатора
- Параметры настройены. Смотреть файлы конфига коммутаторов:
  https://github.com/Ram170107/Otus_practice_ARR/blob/203a68e4e0e2c9d15b7274b66d5d51681ed4a753/labs/lab_3/S1_conf
  https://github.com/Ram170107/Otus_practice_ARR/blob/203a68e4e0e2c9d15b7274b66d5d51681ed4a753/labs/lab_3/S2_conf
  
#### Шаг 7. Создайте VLAN на S1
```
S1#show vlan brief 

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    
100  Clients                          active    Et0/1
200  Management                       active    
999  Parking_Lot                      active    Et0/2, Et0/3
1002 fddi-default                     act/unsup 
1003 token-ring-default               act/unsup 
1004 fddinet-default                  act/unsup 
1005 trnet-default                    act/unsup 

S1#show interfaces trunk 

Port        Mode             Encapsulation  Status        Native vlan
Et0/0       on               802.1q         trunking      1000

Port        Vlans allowed on trunk
Et0/0       100,200

Port        Vlans allowed and active in management domain
Et0/0       100,200

Port        Vlans in spanning tree forwarding state and not pruned
Et0/0       100,200
S1#

```

#### Шаг 8. Назначьте VLAN правильным интерфейсам коммутатора

```

S1#show vlan brief 

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    
100  Clients                          active    Et0/1
200  Management                       active    
999  Parking_Lot                      active    Et0/2, Et0/3
1002 fddi-default                     act/unsup 
1003 token-ring-default               act/unsup 
1004 fddinet-default                  act/unsup 
1005 trnet-default                    act/unsup 

S1#show interfaces trunk 

Port        Mode             Encapsulation  Status        Native vlan
Et0/0       on               802.1q         trunking      1000

Port        Vlans allowed on trunk
Et0/0       100,200

Port        Vlans allowed and active in management domain
Et0/0       100,200

Port        Vlans in spanning tree forwarding state and not pruned
Et0/0       100,200
S1#

```

```
S2#show vlan brief 

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et0/0, Et0/1, Et0/2, Et0/3
1002 fddi-default                     act/unsup 
1003 token-ring-default               act/unsup 
1004 fddinet-default                  act/unsup 
1005 trnet-default                    act/unsup 
S2#

```

- Почему интерфейс Eth0/0 указан в разделе VLAN 1?

```
Все порты по умолчанию находятся в нативном 1 VLAN

```

#### Шаг 9. Вручную настройте интерфейс S1 Eth0/0 в качестве trunk 802.1Q

- Настроено:
```

S1#show interfaces trunk 

Port        Mode             Encapsulation  Status        Native vlan
Et0/0       on               802.1q         trunking      1000

Port        Vlans allowed on trunk
Et0/0       100,200

Port        Vlans allowed and active in management domain
Et0/0       100,200

Port        Vlans in spanning tree forwarding state and not pruned
Et0/0       100,200

```



