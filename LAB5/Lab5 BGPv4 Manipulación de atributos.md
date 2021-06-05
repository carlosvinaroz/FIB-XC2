# Lab5: BGPv4 Manipulación de atributos

## 1. Configurar las direcciones IP, encaminamiento interno (OSPF) y externo (BGP) de la red que aparece en la siguiente figura de forma que funcione correctamente.

````shell
//HB
HB(config)# interface f0/0
HB(config-if)# ip address 146.6.6.1 255.255.255.0
HB(config-if)# ip route 0.0.0.0 0.0.0.0 146.6.6.2
HB(config-if)# no shutdown

//HA
HA(config)# interface f0/0
HA(config-if)# ip address 202.2.2.1 255.255.255.0
HA(config-if)# ip route 0.0.0.0 0.0.0.0 202.2.2.2
HA(config-if)# no shutdown

//AS46
R1(config)# interface loopback0
R1(config-if)# ip address 10.0.111.1 255.255.255.252
R1(config-if)# no shutdown
R1(config)# interface f0/0
R1(config-if)# ip address 10.1.1.1 255.255.255.252
R1(config-if)# no shutdown
R1(config)# interface f1/0
R1(config-if)# ip address  10.1.1.9 255.255.255.252
R1(config-if)# no shutdown

R2(config)# interface loopback0
R2(config-if)# ip address 10.0.111.2 255.255.255.252
R2(config-if)# no shutdown
R2(config)# interface f0/0
R2(config-if)# ip address 146.6.6.2 255.255.255.0
R2(config-if)# no shutdown
R2(config)# interface f1/0
R2(config-if)# ip address  10.1.1.5 255.255.255.252
R2(config-if)# no shutdown
R2(config)# interface f1/1
R2(config-if)# ip address  10.1.1.2 255.255.255.252
R2(config-if)# no shutdown

//AS366
R4(config)# interface loopback0
R4(config-if)# ip address 10.0.112.1 255.255.255.252
R4(config-if)# no shutdown
R4(config)# interface f0/0
R4(config-if)# ip address 10.1.1.1 255.255.255.252
R4(config-if)# no shutdown
R4(config)# interface f1/0
R4(config-if)# ip address  10.1.1.10 255.255.255.252
R4(config-if)# no shutdown

R5(config)# interface loopback0
R5(config-if)# ip address 10.0.112.2 255.255.255.252
R5(config-if)# no shutdown
R5(config)# interface f0/0
R5(config-if)# ip address 202.2.2.2 255.255.255.0
R5(config-if)# no shutdown
R5(config)# interface f1/0
R5(config-if)# ip address  10.1.1.6 255.255.255.252
R5(config-if)# no shutdown
R5(config)# interface f1/1
R5(config-if)# ip address  10.1.1.2 255.255.255.252
R5(config-if)# no shutdown

/* OSPF + BGP */
// AS46
R2(config)# router ospf 1
R2(config-router)# passive-interface f0/0
R2(config-router)# passive-interface f1/0
R2(config-router)# network 146.6.6.0 0.0.0.255 area 0
R2(config-router)# network 10.1.1.0 0.0.0.3 area 0
R2(config-router)# network 10.1.1.4 0.0.0.3 area 0 
R2(config-router)# network 10.0.111.0 0.0.0.3 area 0 
R2(config)# router bgp 46
R2(config-router)# neighbor 10.0.111.1 remote-as 46
R2(config-router)# neighbor 10.0.111.1 update-source loopback0
R2(config-router)# neighbor 10.1.1.6 remote-as 366
R2(config-router)# network 146.6.6.0 mask 255.255.255.0

R1(config)# router ospf 2
R1(config-router)# passive-interface f1/0
R1(config-router)# network 10.1.1.0 0.0.0.3 area 0
R1(config-router)# network 10.1.1.8 0.0.0.3 area 0
R1(config-router)# network 10.0.111.0 0.0.0.3 area 0 
R1(config)# router bgp 46
R1(config-router)# neighbor 10.0.111.2 remote-as 46
R1(config-router)# neighbor 10.0.111.2 update-source loopback0
R1(config-router)# neighbor 10.1.1.10 remote-as 366

// AS366
R5(config)# router ospf 1
R5(config-router)# passive-interface f0/0
R5(config-router)# passive-interface f1/0
R5(config-router)# network 202.2.2.0 0.0.0.255 area 0
R5(config-router)# network 10.1.1.0 0.0.0.3 area 0
R5(config-router)# network 10.1.1.4 0.0.0.3 area 0 
R5(config-router)# network 10.0.112.0 0.0.0.3 area 0 
R5(config)# router bgp 366
R5(config-router)# neighbor 10.0.112.1 remote-as 366
R5(config-router)# neighbor 10.0.112.1 update-source loopback0
R5(config-router)# neighbor 10.1.1.5 remote-as 46
R5(config-router)# network 202.2.2.0 mask 255.255.255.0

R4(config)# router ospf 2
R4(config-router)# passive-interface f1/0
R4(config-router)# network 10.1.1.0 0.0.0.3 area 0
R4(config-router)# network 10.1.1.8 0.0.0.3 area 0
R4(config-router)# network 10.0.112.0 0.0.0.3 area 0 
R4(config)# router bgp 366
R4(config-router)# neighbor 10.0.112.2 remote-as 366
R4(config-router)# neighbor 10.0.112.2 update-source loopback0
R4(config-router)# neighbor 10.1.1.9 remote-as 46
````

## 2. Verificar las entradas en las tablas de encaminamiento BGP y comprobar que cada router de un dominio tiene dos rutas posibles para la red publica del otro dominio. Comprobar que se eligue correctamente la mejor ruta entre las dos según los criterios BGP.

````shell
R# show ip bgp
R# show ip bgp paths
````

## 3. Haz un ping desde el host HA al host HB y confirma que el camino que han seguido los paquetes son:

## Echo request: HA -> R5 -> R2 -> HB
Echo reply: HB -> R2 -> R5 -> HA



````shell
HA# traceroute 146.6.6.1
HB# traceroute 202.2.2.1
````

## 4. Nos situamos en el AS46 (no podeis reprogramar manualmente el router del AS366). Escribe los scripts necesarios para que el ping desde el host HA al host HB siga el siguiente camino:

## Echo request: HA -> R5 -> R4 -> R1 -> R2 -> HB
Echo reply: HB -> R2 -> R1 -> R4 -> R5 -> HA



````shell
R2(config)# router bgp 46
R2(config-router)# neighbor 10.1.1.6 route-map My-Map-1 in
R2(config-router)# neighbor 10.1.1.6 route-map My-Map-2 out
/* HB a HA, le bajamos el local-prefrence a R2(in), 
 * pues así la otra ruta a HA tendría local-preference 100 (por defecto)
 * y por lo tanto más preferencia
R2(config)# route-map My-Map-1 permit 10
R2(config)# match ip address 1
R2(config)# set local-preference 50
/* HA a HB, le subimos la metrica a R5 (out), 
 * pues así la otra ruta a HB tendría métrica 0 (por defecto),
 * y por lo tanto más preferencia
R2(config)# route-map My-Map-2 permit 10
R2(config)# match ip address 2
R2(config)# set metric 50
// ACL's
R2(config)# access-list 1 permit 202.2.2.0 0.0.0.255
R2(config)# access-list 2 permit 146.6.6.0 0.0.0.255
````

## 5. Comprobar las tablas de encaminamiento de R1 y R4 y razonar porque ahora solo hay dos entradas.

````shell
R# show ip bgp
R# show ip bgp paths
````

