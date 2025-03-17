## Шаг 4: Проверьте связь.
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
