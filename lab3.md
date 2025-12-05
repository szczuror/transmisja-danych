# Transmisja Danych - OSPF
Autorzy: Igor Kowalik, Wiktor Szymonek

Data: 05.12.2025 

## 1. Podstawowa konfiguracja urządzenia 

### Oto tabela przedstawiająca schemat adresowania zgodnie z wytycznymi zadania.

### Analiza tablic routingu
Celem tej części jest weryfikacja poprawności działania routingu w sieci oraz zrozumienie, w jaki sposób routery podejmują decyzje o przesyłaniu pakietów. Poniżej przedstawiono zrzuty polecenia show ip route dla poszczególnych routerów wraz z analizą kluczowych wpisów.

Routery:
```
R1#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     192.168.11.0/30 is subnetted, 1 subnets
C       192.168.11.0 is directly connected, Ethernet0/0
     192.168.0.0/32 is subnetted, 1 subnets
C       192.168.0.1 is directly connected, Loopback0
R1#
R2#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     192.168.10.0/30 is subnetted, 2 subnets
C       192.168.10.0 is directly connected, Ethernet0/1
C       192.168.10.4 is directly connected, Ethernet0/2
     192.168.11.0/30 is subnetted, 1 subnets
C       192.168.11.0 is directly connected, Ethernet0/0
     192.168.0.0/32 is subnetted, 1 subnets
C       192.168.0.2 is directly connected, Loopback0
R2#

R3#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     192.168.10.0/30 is subnetted, 3 subnets
C       192.168.10.4 is directly connected, Ethernet0/2
C       192.168.10.8 is directly connected, Ethernet0/0
C       192.168.10.12 is directly connected, Ethernet0/3
     192.168.0.0/32 is subnetted, 1 subnets
C       192.168.0.3 is directly connected, Loopback0
R3#

R4#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     192.168.10.0/30 is subnetted, 3 subnets
C       192.168.10.0 is directly connected, Ethernet0/1
C       192.168.10.12 is directly connected, Ethernet0/3
C       192.168.10.16 is directly connected, Ethernet0/2
     192.168.0.0/32 is subnetted, 1 subnets
C       192.168.0.4 is directly connected, Loopback0
R4#

R5#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     192.168.10.0/30 is subnetted, 2 subnets
C       192.168.10.8 is directly connected, Ethernet0/0
C       192.168.10.16 is directly connected, Ethernet0/2
     192.168.0.0/32 is subnetted, 1 subnets
C       192.168.0.5 is directly connected, Loopback0
R5#
```

Wpisy oznaczone literą C informują o sieciach podłączonych bezpośrednio do interfejsów routera. Router wie o nich automatycznie po przypisaniu adresu IP i podniesieniu interfejsu (stan up/up). Na tym etapie routery nie widzą jeszcze sieci odległych (np. R1 nie widzi pętli loopback routera R5), ponieważ nie skonfigurowaliśmy jeszcze protokołu routingu dynamicznego OSPF

## 2. OSPF
