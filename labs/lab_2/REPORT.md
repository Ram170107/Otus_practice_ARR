## Часть 1. Шаг 4: Проверьте связь.
### Проверьте способность комутаторов обмениваться эхо-запросами.
- #### Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S2?	_ДА_

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

- #### Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S3?	_ДА_

```
S1#ping 192.168.1.3
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.3, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
S1#

```

- #### Успешно ли выполняется эхо-запрос от коммутатора S2 на коммутатор S3? _ДА_

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

#### На схеме ниже укзаны роли и состояния (Sts) активных портов на каждом коммутаторе в топологии:

![](https://github.com/Ram170107/Otus_practice_ARR/blob/2c454f246aa113e251d01f496de6893e5a168d2f/labs/lab_2/Step_4_%D0%A1%D1%85%D0%B5%D0%BC%D0%B0.png)

#### Данные протокола spanning tree с комутаторов S1, S2, S3:

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
> _S1_
- #### Почему этот коммутатор был выбран протоколом spanning-tree в качестве корневого моста?
> _MAC адрес данного коммутатора наименьший среди всех_
#### Какие порты на коммутаторе являются корневыми портами? 
> _Корневые Root порты: у S2 - Et0/1 Root, у S3 - Et0/3 Root_
#### Какие порты на коммутаторе являются назначенными портами? 
> _У S1 Et0/1 Desg FWD, Et0/3 Desg FWD, у S2 Et0/3 Desg FWD_
#### Какой порт отображается в качестве альтернативного и в настоящее время заблокирован? 
> _У S3 Et0/1 Altn BLK_
#### Почему протокол spanning-tree выбрал этот порт в качестве невыделенного (заблокированного) порта?
> _Из-за более высокой стоимости линка_


## Часть 3: Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов.
### Часть 1. Определите коммутатор с заблокированным портом.
> _В моём случае это - S3_

```
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
#### Изменили стоимость заблокирвоанного порта:
```
S3(config)#interface ethernet 0/3
S3(config-if)#spanning-tree cost 18

```
#### Видим что стоимость изменилась и переназначились роли портов:
```
S3#show spanning-tree 

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.1000
             Cost        18
             Port        4 (Ethernet0/3)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.3000
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/1               Desg FWD 100       128.2    Shr 
Et0/3               Root FWD 18        128.4    Shr

```
#### Также видим что изменились роли на коммутаторе S2, один из портов стал заблокированным:
```
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
Et0/3               Altn BLK 100       128.4    Shr

```

- #### Почему протокол spanning-tree заменяет ранее заблокированный порт на назначенный порт и блокирует порт, который был назначенным портом на другом коммутаторе?
> _Порт с более низкой стоимостью является более предпочтительным, в моём случае стоимость порта на S2 стала выше_

## Часть 4
Влючили все ранее выключенные порты:

```
S1#show spanning-tree 

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
Et0/0               Desg FWD 100       128.1    Shr 
Et0/1               Desg FWD 100       128.2    Shr 
Et0/2               Desg FWD 100       128.3    Shr 
Et0/3               Desg FWD 100       128.4    Shr 


S2#show spanning-tree 

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.1000
             Cost        100
             Port        1 (Ethernet0/0)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.2000
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Root FWD 100       128.1    Shr 
Et0/1               Altn BLK 100       128.2    Shr 
Et0/2               Desg FWD 100       128.3    Shr 
Et0/3               Desg FWD 100       128.4    Shr 



S3#show spanning-tree 

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.1000
             Cost        100
             Port        3 (Ethernet0/2)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.3000
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Altn BLK 100       128.1    Shr 
Et0/1               Altn BLK 100       128.2    Shr 
Et0/2               Root FWD 100       128.3    Shr 
Et0/3               Altn BLK 100       128.4    Shr 


```

- #### Какой порт выбран протоколом STP в качестве порта корневого моста на каждом коммутаторе некорневого моста?
> _S2 - Et0/0               Root FWD 100       128.1    Shr_
> _S3 - Et0/2               Root FWD 100       128.3    Shr_
- #### Почему протокол STP выбрал эти порты в качестве портов корневого моста на этих коммутаторах?
> _Данные порты выбраны корневыми как наименьшие по нумерации, напрвленные в сторону корневого моста_


## Вопросы для повторения
#### 1. Какое значение протокол STP использует первым после выбора корневого моста, чтобы определить выбор порта?
> _Стоимость порта (линка) (Cost)_
#### 2. Если первое значение на двух портах одинаково, какое следующее значение будет использовать протокол STP при выборе порта?
> _Значение приоритета порта_
#### 3. Если оба значения на двух портах равны, каким будет следующее значение, которое использует протокол STP при выборе порта?
> _Нумерация порта_

