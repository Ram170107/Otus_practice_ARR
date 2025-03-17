## Часть 1. Шаг 4: Проверьте связь.
### Проверьте способность комутаторов обмениваться эхо-запросами.
- #### Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S2?	> ДА

```
S1#show interfaces vlan 1
Vlan1 is up, line protocol is up 
  Hardware is Ethernet SVI, address is aabb.cc80.1000 (bia aabb.cc80.1000)
  Description: Mgmt
  Internet address is 192.168.1.1/24
```

```
S1#ping 192.168.1.2      
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
S1#

```

- #### Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S3?	> ДА

```
S1#ping 192.168.1.3
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.3, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
S1#

```

- #### Успешно ли выполняется эхо-запрос от коммутатора S2 на коммутатор S3?	> ДА

```
S2#show interfaces vlan 1
Vlan1 is up, line protocol is up 
  Hardware is Ethernet SVI, address is aabb.cc80.2000 (bia aabb.cc80.2000)
  Description: Mgmt
  Internet address is 192.168.1.2/24

```

```
S2#ping 192.168.1.3
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.3, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
S2#

```
## Часть 2. Шаг 4: Отобразите данные протокола spanning-tree.

![](https://github.com/Ram170107/Otus_practice_ARR/blob/2c454f246aa113e251d01f496de6893e5a168d2f/labs/lab_2/Step_4_%D0%A1%D1%85%D0%B5%D0%BC%D0%B0.png)


```
S1#sh spanning-tree 

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.1000
             This bridge is the root
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.1000
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/1               Desg FWD 100       128.2    Shr 
Et0/3               Desg FWD 100       128.4    Shr 



S2#show spanning-tree 

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.1000
             Cost        100
             Port        2 (Ethernet0/1)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.2000
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/1               Root FWD 100       128.2    Shr 
Et0/3               Desg FWD 100       128.4    Shr 


S3#show spanning-tree 

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.1000
             Cost        100
             Port        4 (Ethernet0/3)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.3000
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/1               Altn BLK 100       128.2    Shr 
Et0/3               Root FWD 100       128.4    Shr 

```

### С учетом выходных данных, поступающих с коммутаторов, ответьте на следующие вопросы:
- #### Какой коммутатор является корневым мостом?

> S1

- #### Почему этот коммутатор был выбран протоколом spanning-tree в качестве корневого моста?
_______________________________________________________________________________________
_______________________________________________________________________________________
Какие порты на коммутаторе являются корневыми портами? ___________________________________
Какие порты на коммутаторе являются назначенными портами? ________________________________
Какой порт отображается в качестве альтернативного и в настоящее время заблокирован? ____________________________
Почему протокол spanning-tree выбрал этот порт в качестве невыделенного (заблокированного) порта?
_______________________________________________________________________________________
_______________________________________________________________________________________
