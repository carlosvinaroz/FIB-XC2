# LAB3: Multiprotocol Label Switching (MPLS)

## Parte 1

##### 1 . Configurar las direcciones de loopback en R1-R3 como 1.#HA.1.X/32 donde #HA es el número del PC que hace de HA y X es el número del router RX.

````shell
R1# configure terminal
R1(config)# interface loopback0
R1(config-if)# ip address 1.11.1.1 255.255.255.255
R1(config-if)# no shutdown

R2# configure terminal
R2(config)# interface loopback0
R2(config-if)# ip address 1.11.1.2 255.255.255.255
R2(config-if)# no shutdown

R3# configure terminal
R3(config)# interface loopback0
R3(config-if)# ip address 1.11.1.3 255.255.255.255
R3(config-if)# no shutdown
````

##### 2. Configurar las direcciones IP en todas las interfaces de la red que aparece en la figura de forma que hay conectividad entre dos interfaces vecinas. Notar que entre R1 y R2 se usa el enlace serie y también entre los routers R3 de los dos grupos. Recordar que para los enlaces serie hay que configurar la velocidad de transmisión 

````shell
// HA

HA# configure terminal
HA(config)# interface f0/0
HA(config-if)# ip address 145.11.0.2 255.255.255.0
HA(config-if)# no shutdown

// R1

R1# configure terminal
R1(config)# interface f0/0
R1(config-if)# ip address 145.11.0.1 255.255.255.0
R1(config-if)# no shutdown

R1# configure terminal
R1(config)# interface f1/0
R1(config-if)# ip address 10.11.2.1 255.255.255.0
R1(config-if)# no shutdown

R1# configure terminal
R1(config)# interface s2/0
R1(config-if)# ip address 10.11.0.1 255.255.255.0
R1(config-if)# clockrate 56000
R1(config-if)# no shutdown

// R2

R2# configure terminal
R2(config)# interface f0/0
R2(config-if)# ip address 10.11.1.2 255.255.255.0
R2(config-if)# no shutdown

R2# configure terminal
R2(config)# interface f1/0
R2(config-if)# ip address 160.11.0.2 255.255.255.0
R2(config-if)# no shutdown

R2# configure terminal
R2(config)# interface s2/0
R2(config-if)# ip address 10.11.0.2 255.255.255.0
R2(config-if)# no shutdown

// R3

R3# configure terminal
R3(config)# interface f0/0
R3(config-if)# ip address 10.11.1.3 255.255.255.0
R3(config-if)# no shutdown

R3# configure terminal
R3(config)# interface f1/0
R3(config-if)# ip address 10.11.2.3 255.255.255.0
R3(config-if)# no shutdown

// HB

HB# configure terminal
HB(config)# interface f0/0
HB(config-if)# ip address 160.11.0.1 255.255.255.0
HB(config-if)# no shutdown

````



##### 3. Configurar encaminamiento interno (OSPF) en los routers. Distribuir las redes y las loopbacks por OSPF usando una única área. No distribuir OSPF hacia los hosts2. Comprobar que hay conectividad entre los hosts.

````shell
R1# configure terminal
R1(config)# router ospf 1
R1(config-router)# network 10.11.0.0 0.0.0.255 area 0
R1(config-router)# network 145.11.0.0 0.0.0.255 area 0
R1(config-router)# network 10.11.2.0 0.0.0.255 area 0
R1(config-router)# passive-interface FastEthernet0/0

R2# configure terminal
R2(config)# router ospf 2
R2(config-router)# network 10.11.0.0 0.0.0.255 area 0
R2(config-router)# network 160.11.0.0 0.0.0.255 area 0
R2(config-router)# network 10.11.1.0 0.0.0.255 area 0
R2(config-router)# passive-interface FastEthernet1/0

R3# configure terminal
R3(config)# router ospf 3
R3(config-router)# network 10.11.2.0 0.0.0.255 area 0
R3(config-router)# network 10.11.1.0 0.0.0.255 area 0
````

##### 4. Activar MPLS en los routers R1, R2 y R3.

````shell
R1# configure terminal
R1(config)# ip cef
R1(config)# mpls label protocol ldp
R1(config)# interface f0/0
R1(config-if)# mpls ip
R1(config)# interface f1/0
R1(config-if)# mpls ip
R1(config)# interface s2/0
R1(config-if)# mpls ip

R2# configure terminal
R2(config)# ip cef
R2(config)# mpls label protocol ldp
R2(config)# interface f0/0
R2(config-if)# mpls ip
R2(config)# interface f1/0
R2(config-if)# mpls ip
R2(config)# interface s2/0
R2(config-if)# mpls ip

R3# configure terminal
R3(config)# ip cef
R3(config)# mpls label protocol ldp
R3(config)# interface f0/0
R3(config-if)# mpls ip
R3(config)# interface f1/0
R3(config-if)# mpls ip
````

##### 5. Comprobar el estado de MPLS y de LDP y comprobar las tablas de encaminamiento, FIB, LIB y FLIB

````shell
R# show ip cef
R# show mpls ldp bindings
R# show mpls forwarding-table
````



## 

## Parte 2

##### 1. Manteniendo la configuración anterior, activar las extensiones TE en MPLS, en las interfaces (solo las que van hacia otro routers, no las que van a los hosts) y en OSPF como en explicado en la Sección 3.3.2. No configurar el ancho de banda máximo por túnel.

````shell
R1# configure terminal
R1(config)# interface f1/0
R1(config-if)# mpls traffic-eng tunnels
R1(config)# interface s2/0
R1(config-if)# mpls traffic-eng tunnels

R2# configure terminal
R2(config)# interface f0/0
R2(config-if)# mpls traffic-eng tunnels
R2(config)# interface s2/0
R2(config-if)# mpls traffic-eng tunnels

R3# configure terminal
R3(config)# interface f1/0
R3(config-if)# mpls traffic-eng tunnels
R3(config)# interface f0/0
R3(config-if)# mpls traffic-eng tunnels

RX(config)# router ospf 10 
RX(config-router)# mpls traffic-eng area 0
RX(config-router)# mpls traffic-eng router-id loopback0
````

##### 2. Configurar RSVP con ancho de banda máximo de 1000 kbps en las interfaces fastethernet y de 100 kbps en las series como indicado en la Sección 3.3.2. No configurar el ancho de banda máximo por túnel.

```shell
R1# configure terminal
R1(config)# interface f1/0
R1(config-if)# ip rsvp bandwidth 1000
R1(config)# interface s2/0
R1(config-if)# ip rsvp bandwidth 100

R2# configure terminal
R1(config)# interface f0/0
R1(config-if)# ip rsvp bandwidth 1000
R1(config)# interface s2/0
R1(config-if)# ip rsvp bandwidth 100

R1# configure terminal
R1(config)# interface f1/0
R1(config-if)# ip rsvp bandwidth 1000
R1(config)# interface f0/0
R1(config-if)# ip rsvp bandwidth 1000
```

##### 3. Activar el túnel Tunnel1 de 50 kbps entre R1 y R2 entre las interfaces de loopbacks de R1 y R2. Comprobar que el túnel está activo.

````shell
R1(config)# interface tunnel1
R1(config-if)# ip unnumbered loopback0
R1(config-if)# tunnel mode mpls traffic-eng 
R1(config-if)# tunnel destination 1.11.1.2
R1(config-if)# tunnel mpls traffic-eng bandwidth 50
R1(config-if)# tunnel mpls traffic-eng autoroute announce
R1# show mpls traffic-eng tunnels brief
````

##### 4. Activar el túnel Tunnel2 de 100 kbps entre R2 y R1 entre las interfaces de loopbacks de R2 y R1. Comprobar que el túnel está activo.

````shell
R2(config)# interface tunnel2
R2(config-if)# ip unnumbered loopback0
R2(config-if)# tunnel mode mpls traffic-eng 
R2(config-if)# tunnel destination 1.11.1.1
R2(config-if)# tunnel mpls traffic-eng bandwidth 100
R2(config-if)# tunnel mpls traffic-eng autoroute announce
R2# show mpls traffic-eng tunnels brief
````

##### 5. Comprobar, usando Traceroute, que un ping entre HA y HB pasa por los túneles.

````shell

````

