# LAB 2: Intra-Domain Routing: OSPF

## Passos

##### 1. Configurar todas las interfaces de los routers y añadir una interfaz de loopback en cada router.  asignaremos una dirección 10.0.<PC#>.1/30 para el loopback de cada router. Escoger como número C#, el número del ordenador, es decir, si tiene 115, escoger la red 10.0.115.1/30 para la loopback de ese router.

RX

````shell
RX# configure terminal
RX(config)# interface loopback0
RX(config-if)# ip address 10.0.<PC#>.1 255.255.255.252
RX(config-if)# no shutdown
RX(config-if)#exit
RX(config)# interface f0/0
RX(config-if)# ip address ipaddress 255.255.255.0
RX(config-if)# no shutdown
RX(config)# interface f1/0
RX(config-if)# ip address ipaddress_Hub 255.255.255.0
RX(config-if)# no shutdown
````



##### 2. Configurar las IPs de los PCs y una ruta por defecto (usar los comandos ifconfig y route add de Linux). Comprobar con ping que hay conectividad entre interfaces de una misma red.

````shell
PCX#configure terminal
PCX(config)# interface f0/0
PCX(config-if)# ip address ipaddress 255.255.255.0
PCX(config-if)# ip route 0.0.0.0 0.0.0.0 ipaddress_router
PCX(config-if)# no shutdown
````



##### 3. Configurar OSPF (comando network) en los routers en una única área 0. Comprobar con ping que todos los routers se ven.

````shell
RX# configure terminal
RX(config)# router ospf 1
RX(config-router)# network 100.0.0.0 0.0.0.255 area 0
RX(config-router)# network red_router 0.0.0.255 area 0
RX(config-router)# passive-interface FastEthernet0/0
````



##### 4. El PC llamado Internet representa el router del ISP. Configurar la ruta por defecto en R4 y redistribuirla a los demás routers usando OSPF. Comprobar con ping que hay conectividad entre los PCs e Internet.

````shell
R4# configure terminal
R4(config)# ip route 0.0.0.0 0.0.0.0 4.4.4.2 
R4(config)# router ospf 1
R4(config-router)# default-information originate
````



##### 5. Comprobar que en las tablas de encaminamiento de todos los routers aparecen las redes. Comprobar que aparecen correctamente las entradas que corresponden a las redes directamente conectadas y las redes de la misma área. Interpretar las métricas.

````shell

````



##### 6. Usar los comandos de verificación de ospf (por ejemplo show ip ospf interface), e intentar interpretarlos (por ejemplo RouterID, DR y BDR en los enlaces broadcast, routers adyacentes, etc).

````shell

````



##### 7. Probar de desconectar algún enlace y comprobar cómo las tablas se actualizan en pocos segundos (conectarlo otra vez).

````shell

````



##### 8. Hacer un shutdown del enlace DR y comprobar la elección del nuevo DR y del nuevo BDR. Asignar prioridades a alguna de las interfaces para fijar la nueva elección.

````shell

````



## PARTE 2



![image-20201031182449624](C:\Users\Carlos\AppData\Roaming\Typora\typora-user-images\image-20201031182449624.png)
