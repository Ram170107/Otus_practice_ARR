### Разработаете и задокументируете адресное пространство для лабораторного стенда.

![](https://github.com/Ram170107/Otus_practice_ARR/blob/ab9847f9f1dd3ec8429c6e808086fd3aa0cbc5b2/labs/lab_4/%D0%A1%D1%85%D0%B5%D0%BC%D0%B0%20%D0%BB%D0%B0%D0%B1%204.png)

>Общая схема с адресацией

![](https://github.com/Ram170107/Otus_practice_ARR/blob/ab9847f9f1dd3ec8429c6e808086fd3aa0cbc5b2/labs/lab_4/%D0%A2%D0%B0%D0%B1%D0%BB%D0%B8%D1%86%D0%B0%20%D0%B0%D0%B4%D1%80%D0%B5%D1%81%D0%BE%D0%B2%20PC.png)

>Таблица адресов PC

![](https://github.com/Ram170107/Otus_practice_ARR/blob/ab9847f9f1dd3ec8429c6e808086fd3aa0cbc5b2/labs/lab_4/%D0%A2%D0%B0%D0%B1%D0%BB%D0%B8%D1%86%D0%B0%20%D1%81%D0%B2%D0%B8%D1%82%D1%87%D0%B5%D0%B9.png)

>Таблица адресов свитчей

![](https://github.com/Ram170107/Otus_practice_ARR/blob/ab9847f9f1dd3ec8429c6e808086fd3aa0cbc5b2/labs/lab_4/%D0%A2%D0%B0%D0%B1%D0%BB%D0%B8%D1%86%D0%B0_%D0%BC%D0%B0%D1%80%D1%88%D1%80%D1%83%D1%82%D0%B8%D0%B7%D0%B0%D1%82%D0%BE%D1%80%D0%BE%D0%B2.png)

>Таблица адресов маршрутизаторов

### Настроите ip адреса на каждом активном порту
#### IP адреса настроены на всех активных портах.
###### Пример настройки портов:

```interface Loopback0
 ip address 10.1.0.14 255.255.255.255
!         
interface Ethernet0/0
 description TO_R12_eth0/2
 ip address 10.0.140.1 255.255.255.252
!         
interface Ethernet0/1
 description TO_R13_eth0/3
 ip address 10.0.141.1 255.255.255.252
!         
interface Ethernet0/2
 description TO_Kitorn_R22_eth0/0
 ip address 10.0.220.2 255.255.255.252
!         
interface Ethernet0/3
 description TO_R19_eth0/0
 ip address 10.0.143.1 255.255.255.252

```
> На маршрутизаторе R14

```R12#sh ip int br
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet0/0                10.10.120.1     YES NVRAM  up                    up      
Ethernet0/1                10.10.121.1     YES NVRAM  up                    up      
Ethernet0/2                10.0.140.2      YES NVRAM  up                    up      
Ethernet0/3                10.0.151.2      YES NVRAM  up                    up      
Ethernet1/0                unassigned      YES NVRAM  administratively down down    
Ethernet1/1                unassigned      YES NVRAM  administratively down down    
Ethernet1/2                unassigned      YES NVRAM  administratively down down    
Ethernet1/3                unassigned      YES NVRAM  administratively down down    
Loopback0                  10.2.0.12       YES NVRAM  up                    up

```
> Неактивные порты переведены в режим administratively down

### Настроите каждый VPC в каждом офисе в своем VLAN.
##### В Москве настроил для каждой сети свой VLAN интерфейс.
![](https://github.com/Ram170107/Otus_practice_ARR/blob/e6a697049b3748f767796f95fea380e25971a5cf/labs/lab_4/%D0%9C%D0%BE%D1%81%D0%BA%D0%B2%D0%B0%20%D1%81%D0%B5%D1%82%D0%B8.png)
##### Cтерменировав все на SW4-SW5 - они настроены при помощи HSRP.
```interface Loopback0
 ip address 10.3.0.4 255.255.255.255
!         
interface Port-channel1
 switchport trunk allowed vlan 10,20,45
 switchport trunk encapsulation dot1q
 switchport mode trunk
!         
interface Ethernet0/0
 description TO_SW3_eth0/0
 switchport trunk allowed vlan 10,45
 switchport trunk encapsulation dot1q
 switchport mode trunk
!         
interface Ethernet0/1
 description TO_SW2_eth0/1
 switchport trunk allowed vlan 20,45
 switchport trunk encapsulation dot1q
 switchport mode trunk
!         
interface Ethernet0/2
 switchport trunk allowed vlan 10,20,45
 switchport trunk encapsulation dot1q
 switchport mode trunk
 channel-group 1 mode active
!         
interface Ethernet0/3
 switchport trunk allowed vlan 10,20,45
 switchport trunk encapsulation dot1q
 switchport mode trunk
 channel-group 1 mode active
!         
interface Ethernet1/0
 description TO_R12_eth0/0
 no switchport
 ip address 10.10.120.2 255.255.255.252
 duplex auto
!         
interface Ethernet1/1
 description TO_R13_eth0/1
 no switchport
 ip address 10.10.131.2 255.255.255.252
 duplex auto
!         
interface Ethernet1/2
 switchport access vlan 789
 switchport mode access
 shutdown 
!         
interface Ethernet1/3
 switchport access vlan 789
 switchport mode access
 shutdown 
!         
interface Vlan10
 description OTDEL_KADROV
 ip address 192.168.10.2 255.255.255.0
 standby 1 ip 192.168.10.1
 standby 1 timers 1 3
 standby 1 priority 110
 standby 1 preempt
 standby 1 authentication cisco10
 standby 1 track 1 decrement 30
!         
interface Vlan20
 description BUHGALTERIA
 ip address 192.168.20.2 255.255.255.0
 standby 2 ip 192.168.20.1
 standby 2 timers 1 3
 standby 2 priority 110
 standby 2 preempt
 standby 2 authentication cisco20
 standby 2 track 1 decrement 30
!         
interface Vlan45
 description MGMT
 ip address 172.10.45.2 255.255.255.240
 standby 4 ip 172.10.45.1
 standby 4 timers 1 3
 standby 4 priority 110
 standby 4 preempt
 standby 4 authentication cisco45
 standby 4 track 1 decrement 30

```
> Выгрузка настроенных портов и VLAN на SW4

```VPC1> sh ip

NAME        : VPC1[1]
IP/MASK     : 192.168.10.102/24
GATEWAY     : 192.168.10.1
DNS         : 
MAC         : 00:50:79:66:68:01
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500

VPC1> ping 192.168.10.1

84 bytes from 192.168.10.1 icmp_seq=1 ttl=255 time=0.354 ms
84 bytes from 192.168.10.1 icmp_seq=2 ttl=255 time=1.026 ms
84 bytes from 192.168.10.1 icmp_seq=3 ttl=255 time=0.597 ms
84 bytes from 192.168.10.1 icmp_seq=4 ttl=255 time=0.628 ms
84 bytes from 192.168.10.1 icmp_seq=5 ttl=255 time=0.536 ms

VPC1>

```
> Тест ping до шлюза от VPC1

##### В С. Петербурге стерменировано на SW9 на VLAN интерфейсах:

```SW9#sh run | s int
vlan internal allocation policy ascending
interface Loopback0
 ip address 10.20.42.9 255.255.255.255
interface Port-channel2
 switchport trunk allowed vlan 20,30,45
 switchport trunk encapsulation dot1q
 switchport mode trunk
interface Ethernet0/0
 switchport trunk allowed vlan 20,30,45
 switchport trunk encapsulation dot1q
 switchport mode trunk
 channel-group 2 mode active
interface Ethernet0/1
 switchport trunk allowed vlan 20,30,45
 switchport trunk encapsulation dot1q
 switchport mode trunk
 channel-group 2 mode active
interface Ethernet0/2
 description VPC8
 switchport access vlan 30
 switchport mode access
 switchport port-security maximum 2
 switchport port-security
 no cdp enable
 spanning-tree bpduguard enable
 ip dhcp snooping limit rate 10
interface Ethernet0/3
 description TO_R17_eth0/0
 no switchport
 ip address 10.0.170.2 255.255.255.252
 duplex auto
interface Ethernet1/0
 no switchport
 ip address 10.0.162.2 255.255.255.252
 shutdown 
 duplex auto
interface Ethernet1/1
 switchport access vlan 888
 switchport mode access
 shutdown 
interface Ethernet1/2
 switchport access vlan 888
 switchport mode access
 shutdown 
interface Ethernet1/3
 switchport access vlan 888
 switchport mode access
 shutdown 
interface Vlan20
 ip address 192.168.20.5 255.255.255.0
interface Vlan30
 ip address 192.168.30.1 255.255.255.0
interface Vlan45
 description MGMT
 ip address 172.20.45.17 255.255.255.240

```
![](https://github.com/Ram170107/Otus_practice_ARR/blob/e6a697049b3748f767796f95fea380e25971a5cf/labs/lab_4/%D0%9F%D0%B5%D1%82%D0%B5%D1%80%D0%B1%D1%83%D1%80%D0%B3%20%D1%81%D0%B5%D1%82%D0%B8.png)




### Настроите VLAN/Loopbackup interface управления для сетевых устройств
### Настроите сети офисов так, чтобы не возникало broadcast штормов, а использование линков было максимально оптимизировано. Используете IPv4. IPv6 по желанию

> https://github.com/Ram170107/Otus_practice_ARR/blob/184578dc49c6b9a67bdb2e6190929a99021acf9c/labs/lab_4/Lab_4.zip
