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
> Не активные порты переведены в режим administratively down

### Настроите каждый VPC в каждом офисе в своем VLAN.
### Настроите VLAN/Loopbackup interface управления для сетевых устройств
### Настроите сети офисов так, чтобы не возникало broadcast штормов, а использование линков было максимально оптимизировано. Используете IPv4. IPv6 по желанию

> https://github.com/Ram170107/Otus_practice_ARR/blob/184578dc49c6b9a67bdb2e6190929a99021acf9c/labs/lab_4/Lab_4.zip
