# LAB 1

## Parte 1

### Estructura

![image-20201026001238703](C:\Users\Carlos\AppData\Roaming\Typora\typora-user-images\image-20201026001238703.png)

### Passos a seguir

Recuerda siempre luego de configurar una interface hacer 2 exits y finalmente guardar la configuración con wr:

````shell
PC1(config-if)# exit
PC1(config)# exit
PC1# wr
````



##### 1.Configurar la IPv6 de PC1 y PC2 en modo stateless.

PC1 y PC2

````shell
PCX# configure terminal
PCX(config)# interface f0/0
PCX(config-if)# ipv6 address autoconfig
PCX(config-if)# no shutdown
````



##### 2. Activar el ICMP router advertisement en R1 para que configure y configurar la interfaz fastethernet de R1 (la que conecta PC1) con el routing-prefix de la figura y el interfaceID como conversión de la MAC a 64 bits.

R1

````shell
R1(config)# ipv6 unicast-routing
R1(config)# interface f0/0
R1(config-if)# ipv6 address 2001:11::/64 eui-64
R1(config-if)# no shutdown
````

##### 3. Hacer lo mismo para R2.

R2

````shell
R2(config)# ipv6 unicast-routing
R2(config)# interface f0/0
R2(config-if)# ipv6 address 2001:12::/64 eui-64
R2(config-if)# no shutdown
````

##### 4. Configurar las IPv6 de tipo link-local exclusivamente entre R1 y R2

R1

````shell
R1(config)# interface s2/0
R1(config-if)# clock rate 56000
R1(config-if)# ipv6 enable
R1(config-if)# no shutdown
````

R2

````shell
R2(config)# interface s2/0
R2(config-if)# ipv6 enable
R2(config-if)# no shutdown
````

##### 5. Verificar que hay conectividad entre los dos routers y entre los routers y sus respectivos PCs.

````shell
RX# show ipv6 interface
RX# show ipv6 interface brief
RX# ping ipv6address
````

###### R1

````shell
R1# show ipv6 interface
FastEthernet0/0 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::C801:3CFF:FEF4:0
  No Virtual link-local address(es):
  Global unicast address(es):
    2001:11::C801:3CFF:FEF4:0, subnet is 2001:11::/64 [EUI]
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::1:FFF4:0
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
````

````shell
R1# show ipv6 interface brief
FastEthernet0/0        [up/up]
    FE80::C801:3CFF:FEF4:0
    2001:11::C801:3CFF:FEF4:0
Serial2/0              [up/up]
    FE80::C801:3CFF:FEF4:0

````

###### Ping a R2 desde R1

````shell
R1#ping fe80::c801:3cff:fef4:0
Output Interface: Serial2/0
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to FE80::C801:3CFF:FEF4:0, timeout is 2 seconds:
Packet sent with a source address of FE80::C801:3CFF:FEF4:0%Serial2/0
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
````

###### Ping a PC1 desde R1

````shell
R1#ping 2001:11::c801:3cff:fef4:0
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:11::C801:3CFF:FEF4:0, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/2/4 ms
````



###### R2

````shell
R2#show ipv6 interface
FastEthernet0/0 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::C802:36FF:FE84:0
  No Virtual link-local address(es):
  Global unicast address(es):
    2001:12::C802:36FF:FE84:0, subnet is 2001:12::/64 [EUI]
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::1:FF84:0
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
````

````shell
R2#show ipv6 interface brief
FastEthernet0/0        [up/up]
    FE80::C802:36FF:FE84:0
    2001:12::C802:36FF:FE84:0
Serial2/0              [up/up]
    FE80::C802:36FF:FE84:0
````

###### Ping a R1 desde R2

````shell
R2#ping fe80::c802:36ff:fe84:0
Output Interface: Serial2/0
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to FE80::C802:36FF:FE84:0, timeout is 2 seconds:
Packet sent with a source address of FE80::C802:36FF:FE84:0%Serial2/0
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
````

###### Ping a PC2 desde R2

````shell
R2#ping 2001:12::c802:36ff:fe84:0
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:12::C802:36FF:FE84:0, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
````



##### 6. Activar RIPng en todos los routers.

R1 y R2

````shell
RX# configure terminal
RX(config)# ipv6 unicast-routing
RX(config)# ipv6 router rip procesorip
RX(config)# interface f0/0
RX(config-if)#ipv6 rip procesorip enable
RX(config-if)#exit
RX(config)#interface s2/0
RX(config-if)#ipv6 rip procesorip enable
````





##### 7.Comprobar que en las tablas de encaminamiento de todos los routers aparecen los prefijos. Comprobar que aparecen correctamente las entradas que corresponden a las redes directamente conectadas y las redes aprendidas por RIPng.

````shell
RX# show ipv6 route
RX# show ipv6 route rip
````

R1

````shell
R1#show ipv6 route
IPv6 Routing Table - default - 4 entries
C   2001:11::/64 [0/0]
     via FastEthernet0/0, directly connected
L   2001:11::C801:3CFF:FEF4:0/128 [0/0]
     via FastEthernet0/0, receive
R   2001:12::/64 [120/2]
     via FE80::C802:36FF:FE84:0, Serial2/0
L   FF00::/8 [0/0]
     via Null0, receive
````

````shell
R1#show ipv6 route rip
IPv6 Routing Table - default - 4 entries
R   2001:12::/64 [120/2]
     via FE80::C802:36FF:FE84:0, Serial2/0
````



R2

````shell
R2#show ipv6 route
IPv6 Routing Table - default - 4 entries
R   2001:11::/64 [120/2]
     via FE80::C801:3CFF:FEF4:0, Serial2/0
C   2001:12::/64 [0/0]
     via FastEthernet0/0, directly connected
L   2001:12::C802:36FF:FE84:0/128 [0/0]
     via FastEthernet0/0, receive
L   FF00::/8 [0/0]
     via Null0, receive
````

````shell
R2#show ipv6 route rip
IPv6 Routing Table - default - 4 entries
R   2001:11::/64 [120/2]
     via FE80::C801:3CFF:FEF4:0, Serial2/0
````



##### 8. Comprobar que hay conectividad entre cualquier pareja de PCs.

###### Ping a PC2 desde PC1

````shell
PC1#ping 2001:12::c802:36ff:fe84:0
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:12::C802:36FF:FE84:0, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 112/152/256 ms
````

###### Ping a PC1 desde PC2

````shell
PC2#ping 2001:11::c801:3cff:fef4:0
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:11::C801:3CFF:FEF4:0, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 124/154/240 ms
````



##### 9. Usar los comandos de verificación de RIPng e intentar interpretar los resultados.

R1

````shell
R1#debug ipv6 rip
  RIP Routing Protocol debugging is on
R1#
*Oct 26 00:10:37.915: RIPng: Sending multicast update on Serial2/0 for procesorip
*Oct 26 00:10:37.919:        src=FE80::C801:3CFF:FEF4:0
*Oct 26 00:10:37.919:        dst=FF02::9 (Serial2/0)
*Oct 26 00:10:37.923:        sport=521, dport=521, length=32
*Oct 26 00:10:37.923:        command=2, version=1, mbz=0, #rte=1
*Oct 26 00:10:37.923:        tag=0, metric=1, prefix=2001:11::/64
*Oct 26 00:10:37.927: RIPng: Sending multicast update on FastEthernet0/0 for procesorip
*Oct 26 00:10:37.927:        src=FE80::C801:3CFF:FEF4:0
*Oct 26 00:10:37.927:        dst=FF02::9 (FastEthernet0/0)
*Oct 26 00:10:37.927:        sport=521, dport=521, length=52
*Oct 26 00:10:37.927:        command=2, version=1, mbz=0, #rte=2
*Oct 26 00:10:37.927:        tag=0, metric=1, prefix=2001:11::/64
*Oct 26 00:10:37.927:        tag=0, metric=2, prefix=2001:12::/64
*Oct 26 00:10:37.955: RIPng: Packet waiting
R1#t 26 00:10:37.955: RIPng: Process procesorrip received response for procesorip on Serial2/0
*Oct 26 00:10:37.959: RIPng: Packet waiting
*Oct 26 00:10:37.963: RIPng: response received from FE80::C802:36FF:FE84:0 on Serial2/0 for procesorip
*Oct 26 00:10:37.963:        src=FE80::C802:36FF:FE84:0 (Serial2/0)
*Oct 26 00:10:37.967:        dst=FF02::9
*Oct 26 00:10:37.967:        sport=521, dport=521, length=32
*Oct 26 00:10:37.967:        command=2, version=1, mbz=0, #rte=1
*Oct 26 00:10:37.967:        tag=0, metric=1, prefix=2001:12::/64

````

Como hemos visto en el apartado **7**, cada Router tiene un enlace Rip con el PCX que no esta conectado directamente. Con el comando **debug ipv6 rip** podemos ver el intercambio de mensajes RIPng de R1 en este caso. Podemos observar como R1 envía un mensaje RIP por la interface f1/0 y recibe una respuesta de PC2 con la métrica correspondiente y su IPv6. Con esta información se actualiza la tabla de enrutamiento de R1. Y ahora R1 sabe cual es la mejor ruta para llegar a PC2. Mismo caso para R2 y PC1.



## Parte 2

### Estructura

### Passos

##### 1. Configurar la IPv6 global de PC3 y una ruta por defecto.

PC3

````shell
PC3#configure terminal
PC3(config)# interface f0/0
PC3(config-if)# ipv6 address 2001:13::10/64
PC3(config-if)# ipv6 route ::/0 f1/0 
PC3(config-if)# no shutdown
````



##### 2. Configurar la IPv6 global de R1 hacía PC3 y activar el RIP también en esta interfaz.

R1

````shell
R1#configure terminal
R1(config)# ipv6 unicast-routing
R1(config)# ipv6 router rip procesorip
R1(config)#interface f1/0
R1(config-if)#ipv6 address 2001:13::1/64
R1(config-if)#ipv6 rip procesorip enable
R1(config-if)# no shutdown
````



##### 3. Comprobar que hay conectividad entre todos los PCs y que las tablas de encaminamiento de los dos routers son correctas.

###### Ping a PC2 desde PC3

````shell
PC3#ping 2001:12::c802:36ff:fe84:0
````



###### Ping a PC1 desde PC3

````shell
PC3#ping 2001:11::c801:3cff:fef4:0
````



###### Tablas de encaminamiento de los routers

````shell
R1#show ipv6 route
IPv6 Routing Table - default - 6 entries
C   2001:11::/64 [0/0]
     via FastEthernet0/0, directly connected
L   2001:11::C801:3CFF:FEF4:0/128 [0/0]
     via FastEthernet0/0, receive
R   2001:12::/64 [120/2]
     via FE80::C802:36FF:FE84:0, Serial2/0
C   2001:13::/64 [0/0]
     via FastEthernet1/0, directly connected
L   2001:13::1/128 [0/0]
     via FastEthernet1/0, receive
L   FF00::/8 [0/0]
     via Null0, receive
````







