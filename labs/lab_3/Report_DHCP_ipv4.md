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

- На этом этапе какой IP-адрес был бы у ПК, если бы они были подключены к сети с использованием DHCP?

```
IP адрес ПК не присвоился бы, т.к. на маршрутизаторах не поднят DHCP  сервер

```

# Часть 2. Настройте и верифицируйте два сервера DHCP на R1
#### Шаг 1. Настройте R1 с использованием пулов DHCPv4 для двух поддерживаемых подсетей.

```
R1#
ip dhcp excluded-address 192.168.1.1 192.168.1.5
ip dhcp excluded-address 192.168.1.97 192.168.1.102
!         
ip dhcp pool R2_Client_LAN
 network 192.168.1.96 255.255.255.240
 domain-name ccna-lab.com
 default-router 192.168.1.97 
 lease 2 12 30
!         
ip dhcp pool R1_DHCP_Subnet_A
 network 192.168.1.0 255.255.255.192
 default-router 192.168.1.1 
 domain-name ccna-lab.com
 lease 2 12 30

```

#### Шаг 2. Сохраните вашу конфигурацию
#### Шаг 3. Проверьте конфигурацию сервера DHCPv4

```

R1#show ip dhcp pool

Pool R2_Client_LAN :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 14
 Leased addresses               : 1
 Pending event                  : none
 1 subnet is currently in the pool :
 Current index        IP address range                    Leased addresses
 192.168.1.104        192.168.1.97     - 192.168.1.110     1

Pool R1_DHCP_Subnet_A :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 62
 Leased addresses               : 1
 Pending event                  : none
 1 subnet is currently in the pool :
 Current index        IP address range                    Leased addresses
 192.168.1.7          192.168.1.1      - 192.168.1.62      1
R1#

R1#show ip dhcp binding 
Bindings from all pools not associated with VRF:
IP address          Client-ID/	 	    Lease expiration        Type
		    Hardware address/
		    User name
192.168.1.6         0100.5079.6668.05       Apr 22 2025 05:44 AM    Automatic
192.168.1.103       0100.5079.6668.06       Apr 22 2025 05:45 AM    Automatic
R1#
R1#show ip dhcp server statistics 
Memory usage         42096
Address pools        2
Database agents      0
Automatic bindings   2
Manual bindings      0
Expired bindings     0
Malformed messages   0
Secure arp entries   0

Message              Received
BOOTREQUEST          0
DHCPDISCOVER         5
DHCPREQUEST          3
DHCPDECLINE          0
DHCPRELEASE          0
DHCPINFORM           0

Message              Sent
BOOTREPLY            0
DHCPOFFER            3
DHCPACK              3
DHCPNAK              0
R1#




```

#### Шаг 4. Попытка получить IP адрес DHCP на PC-A

```
PC-A> ip dhcp
DORA IP 192.168.1.6/26 GW 192.168.1.1

PC-A> show ip

NAME        : PC-A[1]
IP/MASK     : 192.168.1.6/26
GATEWAY     : 192.168.1.1
DNS         : 
DHCP SERVER : 192.168.1.1
DHCP LEASE  : 217793, 217800/108900/190575
DOMAIN NAME : ccna-lab.com
MAC         : 00:50:79:66:68:05
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500

PC-A> 

```

# Часть 3. Настройте и проверьте DHCP-ретранслятор на R2
#### Шаг 1. Настройте R2 в качестве агента ретрансляции DHCP для локальной сети на Eth0/1

```
R2#
interface Ethernet0/1
 ip address 192.168.1.97 255.255.255.240
 ip helper-address 10.0.0.1

```

#### Шаг 2. Попытка получить IP адрес из DHCP на PC-B

```
PC-B> ip dhcp
DORA IP 192.168.1.103/28 GW 192.168.1.97

PC-B> show ip

NAME        : PC-B[1]
IP/MASK     : 192.168.1.103/28
GATEWAY     : 192.168.1.97
DNS         : 
DHCP SERVER : 10.0.0.1
DHCP LEASE  : 217795, 217800/108900/190575
DOMAIN NAME : ccna-lab.com
MAC         : 00:50:79:66:68:06
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500

PC-B> 

```

- Проверьте подключение, отправив запрос на ip-адрес интерфеса R1 Eth0/1

Для начало настроим ip-адрес на R1 Eth0/1

```
!         
interface Ethernet0/0
 ip address 10.0.0.1 255.255.255.252
!         
interface Ethernet0/1
 ip address 10.0.0.5 255.255.255.252

```

Теперь можем пропинговать оба интерфейса маршрутизатора:

```
PC-B> ping 10.0.0.1

84 bytes from 10.0.0.1 icmp_seq=1 ttl=254 time=0.535 ms
84 bytes from 10.0.0.1 icmp_seq=2 ttl=254 time=0.769 ms
84 bytes from 10.0.0.1 icmp_seq=3 ttl=254 time=0.712 ms
84 bytes from 10.0.0.1 icmp_seq=4 ttl=254 time=0.700 ms
84 bytes from 10.0.0.1 icmp_seq=5 ttl=254 time=0.720 ms

PC-B> ping 10.0.0.5

84 bytes from 10.0.0.5 icmp_seq=1 ttl=254 time=0.728 ms
84 bytes from 10.0.0.5 icmp_seq=2 ttl=254 time=0.683 ms
84 bytes from 10.0.0.5 icmp_seq=3 ttl=254 time=0.778 ms
84 bytes from 10.0.0.5 icmp_seq=4 ttl=254 time=0.676 ms
84 bytes from 10.0.0.5 icmp_seq=5 ttl=254 time=0.697 ms

PC-B> 

```

- Проверка привязок на R1

```
R1#show ip dhcp binding 
Bindings from all pools not associated with VRF:
IP address          Client-ID/	 	    Lease expiration        Type
		    Hardware address/
		    User name
192.168.1.6         0100.5079.6668.05       Apr 22 2025 07:49 AM    Automatic
192.168.1.103       0100.5079.6668.06       Apr 22 2025 07:54 AM    Automatic
R1#

```

- Выведение статистики dhcp на R1 и R2 для проверки сообщений dhcp

```
R1#show ip dhcp server statistics 
Memory usage         42096
Address pools        2
Database agents      0
Automatic bindings   2
Manual bindings      0
Expired bindings     0
Malformed messages   0
Secure arp entries   0

Message              Received
BOOTREQUEST          0
DHCPDISCOVER         8
DHCPREQUEST          6
DHCPDECLINE          0
DHCPRELEASE          0
DHCPINFORM           0

Message              Sent
BOOTREPLY            0
DHCPOFFER            6
DHCPACK              6
DHCPNAK              0
R1#

```
```
R2#show ip dhcp server statistics 
Memory usage         22565
Address pools        0
Database agents      0
Automatic bindings   0
Manual bindings      0
Expired bindings     0
Malformed messages   0
Secure arp entries   0

Message              Received
BOOTREQUEST          0
DHCPDISCOVER         0
DHCPREQUEST          0
DHCPDECLINE          0
DHCPRELEASE          0
DHCPINFORM           0

Message              Sent
BOOTREPLY            0
DHCPOFFER            0
DHCPACK              0
DHCPNAK              0
R2#

```
