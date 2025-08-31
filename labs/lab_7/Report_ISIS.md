## Настроить IS-IS офисе Триада
![](https://github.com/Ram170107/Otus_practice_ARR/blob/ac46155ea26409ecd94684ed0a846d6fb9ae19c4/labs/lab_7/isis.png)
> Область сети для настройки протокола ISIS

#### Настроите IS-IS в ISP Триада.
#### R23 и R25 находятся в зоне 2222.

```
R23#sh run | s isis
 ip router isis 2325
 isis network point-to-point 
 ip router isis 2325
 isis network point-to-point 
router isis 2325
 net 49.2222.0010.0052.0023.00
 is-type level-2-only
R23#sh isis ne
R23#sh isis neighbors 

Tag 2325:
System Id      Type Interface   IP Address      State Holdtime Circuit Id
R24            L2   Et0/2       10.0.232.2      UP    20       01
R25            L2   Et0/1       10.0.231.2      UP    28       00
R23#

```

```
R25#sh run | s isis
 ip router isis 2523
 isis network point-to-point 
 ip router isis 2523
 isis network point-to-point 
router isis 2523
 net 49.2222.0010.0052.0025.00
 is-type level-2-only
R25#sh isis ne
R25#sh isis neighbors 

Tag 2523:
System Id      Type Interface   IP Address      State Holdtime Circuit Id
R23            L2   Et0/0       10.0.231.1      UP    28       00
R26            L2   Et0/2       10.0.252.2      UP    28       01
R25#

```
#### Как мы видим из выгрузки с маршрутизаторов R23 и R25 на них настроен протокол ISIS.
- В строке значение после первой точки указывает нам номер area -  net 49.**2222**.0010.0052.0025.00
#### R24 находится в зоне 24.
#### R26 находится в зоне 26.
