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

```

R1(config-router)#do show ip ospf
 Routing Process "ospf 1" with ID 192.168.0.1
 Start time: 00:09:28.308, Time elapsed: 00:02:24.668
 Supports only single TOS(TOS0) routes
 Supports opaque LSA
 Supports Link-local Signaling (LLS)
 Supports area transit capability
 Router is not originating router-LSAs with maximum metric
 Initial SPF schedule delay 5000 msecs
 Minimum hold time between two consecutive SPFs 10000 msecs
 Maximum wait time between two consecutive SPFs 10000 msecs
 Incremental-SPF disabled
 Minimum LSA interval 5 secs
 Minimum LSA arrival 1000 msecs
 LSA group pacing timer 240 secs
 Interface flood pacing timer 33 msecs
 Retransmission pacing timer 66 msecs
 Number of external LSA 0. Checksum Sum 0x000000
 Number of opaque AS LSA 0. Checksum Sum 0x000000
 Number of DCbitless external and opaque AS LSA 0
 Number of DoNotAge external and opaque AS LSA 0
 Number of areas in this router is 1. 1 normal 0 stub 0 nssa
 Number of areas transit capable is 0
 External flood list length 0
    Area BACKBONE(0)
        Number of interfaces in this area is 2 (1 loopback)
        Area has no authentication
        SPF algorithm last executed 00:00:03.944 ago
        SPF algorithm executed 6 times
        Area ranges are
        Number of LSA 10. Checksum Sum 0x03410C
        Number of opaque link LSA 0. Checksum Sum 0x000000
        Number of DCbitless LSA 0
        Number of indication LSA 0
        Number of DoNotAge LSA 0
        Flood list length 0
```
```
R1(config-router)#do show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     192.168.10.0/30 is subnetted, 5 subnets
O       192.168.10.0 [110/20] via 192.168.11.2, 00:00:08, Ethernet0/0
O       192.168.10.4 [110/20] via 192.168.11.2, 00:00:08, Ethernet0/0
O       192.168.10.8 [110/30] via 192.168.11.2, 00:00:08, Ethernet0/0
O       192.168.10.12 [110/30] via 192.168.11.2, 00:00:08, Ethernet0/0
O       192.168.10.16 [110/30] via 192.168.11.2, 00:00:08, Ethernet0/0
     192.168.11.0/30 is subnetted, 1 subnets
C       192.168.11.0 is directly connected, Ethernet0/0
     192.168.0.0/32 is subnetted, 5 subnets
C       192.168.0.1 is directly connected, Loopback0
O       192.168.0.2 [110/11] via 192.168.11.2, 00:00:09, Ethernet0/0
O       192.168.0.3 [110/21] via 192.168.11.2, 00:00:09, Ethernet0/0
O       192.168.0.4 [110/21] via 192.168.11.2, 00:00:09, Ethernet0/0
O       192.168.0.5 [110/31] via 192.168.11.2, 00:00:11, Ethernet0/0
R1(config-router)#
```

## 3. Baza danych OSPF

```R5#show ip ospf database

            OSPF Router with ID (192.168.0.5) (Process ID 1)

                Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
192.168.0.1     192.168.0.1     554         0x80000003 0x001ECD 2
192.168.0.2     192.168.0.2     95          0x80000007 0x008B48 6
192.168.0.3     192.168.0.3     56          0x80000008 0x008E1B 6
192.168.0.4     192.168.0.4     30          0x80000008 0x00128B 6
192.168.0.5     192.168.0.5     13          0x80000007 0x00BAEB 5

                Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
192.168.10.14   192.168.0.4     516         0x80000001 0x00C9AA
192.168.11.2    192.168.0.2     553         0x80000001 0x001372
```
```
R5#show ip ospf database router

            OSPF Router with ID (192.168.0.5) (Process ID 1)

                Router Link States (Area 0)

  LS age: 560
  Options: (No TOS-capability, DC)
  LS Type: Router Links
  Link State ID: 192.168.0.1
  Advertising Router: 192.168.0.1
  LS Seq Number: 80000003
  Checksum: 0x1ECD
  Length: 48
  Number of Links: 2

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.0.1
     (Link Data) Network Mask: 255.255.255.255
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

    Link connected to: a Transit Network
     (Link ID) Designated Router address: 192.168.11.2
     (Link Data) Router Interface address: 192.168.11.1
      Number of TOS metrics: 0
       TOS 0 Metrics: 10


  LS age: 104
  Options: (No TOS-capability, DC)
  LS Type: Router Links
  Link State ID: 192.168.0.2
  Advertising Router: 192.168.0.2
  LS Seq Number: 80000007
  Checksum: 0x8B48
  Length: 96
  Number of Links: 6

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.0.2
     (Link Data) Network Mask: 255.255.255.255
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 192.168.0.3
     (Link Data) Router Interface address: 192.168.10.5
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.10.4
     (Link Data) Network Mask: 255.255.255.252
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 192.168.0.4
     (Link Data) Router Interface address: 192.168.10.1
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.10.0
     (Link Data) Network Mask: 255.255.255.252
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: a Transit Network
     (Link ID) Designated Router address: 192.168.11.2
     (Link Data) Router Interface address: 192.168.11.2
      Number of TOS metrics: 0
       TOS 0 Metrics: 10


  LS age: 75
  Options: (No TOS-capability, DC)
  LS Type: Router Links
  Link State ID: 192.168.0.3
  Advertising Router: 192.168.0.3
  LS Seq Number: 80000008
  Checksum: 0x8E1B
  Length: 96
  Number of Links: 6

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.0.3
     (Link Data) Network Mask: 255.255.255.255
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

    Link connected to: a Transit Network
     (Link ID) Designated Router address: 192.168.10.14
     (Link Data) Router Interface address: 192.168.10.13
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 192.168.0.5
     (Link Data) Router Interface address: 192.168.10.9
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.10.8
     (Link Data) Network Mask: 255.255.255.252
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 192.168.0.2
     (Link Data) Router Interface address: 192.168.10.6
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.10.4
     (Link Data) Network Mask: 255.255.255.252
      Number of TOS metrics: 0
       TOS 0 Metrics: 10


  LS age: 57
  Options: (No TOS-capability, DC)
  LS Type: Router Links
  Link State ID: 192.168.0.4
  Advertising Router: 192.168.0.4
  LS Seq Number: 80000008
  Checksum: 0x128B
  Length: 96
  Number of Links: 6

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.0.4
     (Link Data) Network Mask: 255.255.255.255
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 192.168.0.5
     (Link Data) Router Interface address: 192.168.10.17
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.10.16
     (Link Data) Network Mask: 255.255.255.252
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: a Transit Network
     (Link ID) Designated Router address: 192.168.10.14
     (Link Data) Router Interface address: 192.168.10.14
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 192.168.0.2
     (Link Data) Router Interface address: 192.168.10.2
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.10.0
     (Link Data) Network Mask: 255.255.255.252
      Number of TOS metrics: 0
       TOS 0 Metrics: 10


  LS age: 45
  Options: (No TOS-capability, DC)
  LS Type: Router Links
  Link State ID: 192.168.0.5
  Advertising Router: 192.168.0.5
  LS Seq Number: 80000007
  Checksum: 0xBAEB
  Length: 84
  Number of Links: 5

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.0.5
     (Link Data) Network Mask: 255.255.255.255
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 192.168.0.4
     (Link Data) Router Interface address: 192.168.10.18
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.10.16
     (Link Data) Network Mask: 255.255.255.252
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 192.168.0.3
     (Link Data) Router Interface address: 192.168.10.10
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.10.8
     (Link Data) Network Mask: 255.255.255.252
      Number of TOS metrics: 0
       TOS 0 Metrics: 10
```
```
R5#show ip ospf database network

            OSPF Router with ID (192.168.0.5) (Process ID 1)

                Net Link States (Area 0)

  Routing Bit Set on this LSA
  LS age: 564
  Options: (No TOS-capability, DC)
  LS Type: Network Links
  Link State ID: 192.168.10.14 (address of Designated Router)
  Advertising Router: 192.168.0.4
  LS Seq Number: 80000001
  Checksum: 0xC9AA
  Length: 32
  Network Mask: /30
        Attached Router: 192.168.0.4
        Attached Router: 192.168.0.3

  Routing Bit Set on this LSA
  LS age: 601
  Options: (No TOS-capability, DC)
  LS Type: Network Links
  Link State ID: 192.168.11.2 (address of Designated Router)
  Advertising Router: 192.168.0.2
  LS Seq Number: 80000001
  Checksum: 0x1372
  Length: 32
  Network Mask: /30
        Attached Router: 192.168.0.2
        Attached Router: 192.168.0.1

R5#
```

## 4

Router 1
```
R1#show ip ospf database

            OSPF Router with ID (192.168.0.1) (Process ID 1)

                Router Link States (Area 1)

Link ID         ADV Router      Age         Seq#       Checksum Link count
192.168.0.1     192.168.0.1     43          0x80000003 0x001ECD 2
192.168.0.2     192.168.0.2     44          0x80000002 0x008FD4 1

                Net Link States (Area 1)

Link ID         ADV Router      Age         Seq#       Checksum
192.168.11.2    192.168.0.2     44          0x80000001 0x001372

                Summary Net Link States (Area 1)

Link ID         ADV Router      Age         Seq#       Checksum
192.168.0.2     192.168.0.2     84          0x80000001 0x00B5AF
192.168.0.3     192.168.0.2     84          0x80000001 0x00104A
192.168.0.4     192.168.0.2     84          0x80000001 0x000653
192.168.0.5     192.168.0.2     84          0x80000001 0x0060ED
192.168.10.0    192.168.0.2     84          0x80000001 0x00A3B3
192.168.10.4    192.168.0.2     84          0x80000001 0x007BD7
192.168.10.8    192.168.0.2     84          0x80000001 0x00B78D
192.168.10.12   192.168.0.2     87          0x80000001 0x008FB1
192.168.10.16   192.168.0.2     88          0x80000001 0x0067D5
```
```
R1#show ip ospf database summary

            OSPF Router with ID (192.168.0.1) (Process ID 1)

                Summary Net Link States (Area 1)

  Routing Bit Set on this LSA
  LS age: 92
  Options: (No TOS-capability, DC, Upward)
  LS Type: Summary Links(Network)
  Link State ID: 192.168.0.2 (summary Network Number)
  Advertising Router: 192.168.0.2
  LS Seq Number: 80000001
  Checksum: 0xB5AF
  Length: 28
  Network Mask: /32
        TOS: 0  Metric: 1

  Routing Bit Set on this LSA
  LS age: 92
  Options: (No TOS-capability, DC, Upward)
  LS Type: Summary Links(Network)
  Link State ID: 192.168.0.3 (summary Network Number)
  Advertising Router: 192.168.0.2
  LS Seq Number: 80000001
  Checksum: 0x104A
  Length: 28
  Network Mask: /32
        TOS: 0  Metric: 11

  Routing Bit Set on this LSA
  LS age: 96
  Options: (No TOS-capability, DC, Upward)
  LS Type: Summary Links(Network)
  Link State ID: 192.168.0.4 (summary Network Number)
  Advertising Router: 192.168.0.2
  LS Seq Number: 80000001
  Checksum: 0x653
  Length: 28
  Network Mask: /32
        TOS: 0  Metric: 11

  Routing Bit Set on this LSA
  LS age: 98
  Options: (No TOS-capability, DC, Upward)
  LS Type: Summary Links(Network)
  Link State ID: 192.168.0.5 (summary Network Number)
  Advertising Router: 192.168.0.2
  LS Seq Number: 80000001
  Checksum: 0x60ED
  Length: 28
  Network Mask: /32
        TOS: 0  Metric: 21

  Routing Bit Set on this LSA
  LS age: 100
  Options: (No TOS-capability, DC, Upward)
  LS Type: Summary Links(Network)
  Link State ID: 192.168.10.0 (summary Network Number)
  Advertising Router: 192.168.0.2
  LS Seq Number: 80000001
  Checksum: 0xA3B3
  Length: 28
  Network Mask: /30
        TOS: 0  Metric: 10

  Routing Bit Set on this LSA
  LS age: 101
  Options: (No TOS-capability, DC, Upward)
  LS Type: Summary Links(Network)
  Link State ID: 192.168.10.4 (summary Network Number)
  Advertising Router: 192.168.0.2
  LS Seq Number: 80000001
  Checksum: 0x7BD7
  Length: 28
  Network Mask: /30
        TOS: 0  Metric: 10

  Routing Bit Set on this LSA
  LS age: 102
  Options: (No TOS-capability, DC, Upward)
  LS Type: Summary Links(Network)
  Link State ID: 192.168.10.8 (summary Network Number)
  Advertising Router: 192.168.0.2
  LS Seq Number: 80000001
  Checksum: 0xB78D
  Length: 28
  Network Mask: /30
        TOS: 0  Metric: 20

  Routing Bit Set on this LSA
  LS age: 104
  Options: (No TOS-capability, DC, Upward)
  LS Type: Summary Links(Network)
  Link State ID: 192.168.10.12 (summary Network Number)
  Advertising Router: 192.168.0.2
  LS Seq Number: 80000001
  Checksum: 0x8FB1
  Length: 28
  Network Mask: /30
        TOS: 0  Metric: 20

  Routing Bit Set on this LSA
  LS age: 104
  Options: (No TOS-capability, DC, Upward)
  LS Type: Summary Links(Network)
  Link State ID: 192.168.10.16 (summary Network Number)
  Advertising Router: 192.168.0.2
  LS Seq Number: 80000001
  Checksum: 0x67D5
  Length: 28
  Network Mask: /30
        TOS: 0  Metric: 20

R1#
```
Router 5
```
R5#show ip ospf database network

            OSPF Router with ID (192.168.0.5) (Process ID 1)

                Net Link States (Area 0)

  Routing Bit Set on this LSA
  LS age: 564
  Options: (No TOS-capability, DC)
  LS Type: Network Links
  Link State ID: 192.168.10.14 (address of Designated Router)
  Advertising Router: 192.168.0.4
  LS Seq Number: 80000001
  Checksum: 0xC9AA
  Length: 32
  Network Mask: /30
        Attached Router: 192.168.0.4
        Attached Router: 192.168.0.3

  Routing Bit Set on this LSA
  LS age: 601
  Options: (No TOS-capability, DC)
  LS Type: Network Links
  Link State ID: 192.168.11.2 (address of Designated Router)
  Advertising Router: 192.168.0.2
  LS Seq Number: 80000001
  Checksum: 0x1372
  Length: 32
  Network Mask: /30
        Attached Router: 192.168.0.2
        Attached Router: 192.168.0.1
```
```
R5#show ip ospf database

            OSPF Router with ID (192.168.0.5) (Process ID 1)

                Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
192.168.0.1     192.168.0.1     1060        0x80000003 0x001ECD 2
192.168.0.2     192.168.0.2     111         0x80000009 0x006175 5
192.168.0.3     192.168.0.3     562         0x80000008 0x008E1B 6
192.168.0.4     192.168.0.4     536         0x80000008 0x00128B 6
192.168.0.5     192.168.0.5     519         0x80000007 0x00BAEB 5

                Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
192.168.10.14   192.168.0.4     1021        0x80000001 0x00C9AA

                Summary Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
192.168.0.1     192.168.0.2     69          0x80000001 0x002438
192.168.11.0    192.168.0.2     109         0x80000001 0x0098BD
R5#
R5#
R5#show ip ospf database summary

            OSPF Router with ID (192.168.0.5) (Process ID 1)

                Summary Net Link States (Area 0)

  Routing Bit Set on this LSA
  LS age: 74
  Options: (No TOS-capability, DC, Upward)
  LS Type: Summary Links(Network)
  Link State ID: 192.168.0.1 (summary Network Number)
  Advertising Router: 192.168.0.2
  LS Seq Number: 80000001
  Checksum: 0x2438
  Length: 28
  Network Mask: /32
        TOS: 0  Metric: 11

  Routing Bit Set on this LSA
  LS age: 115
  Options: (No TOS-capability, DC, Upward)
  LS Type: Summary Links(Network)
  Link State ID: 192.168.11.0 (summary Network Number)
  Advertising Router: 192.168.0.2
  LS Seq Number: 80000001
  Checksum: 0x98BD
  Length: 28
  Network Mask: /30
        TOS: 0  Metric: 10

R5#
```

Show ip route dla kazdego
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



     192.168.10.0/30 is subnetted, 5 subnets

O IA    192.168.10.0 [110/20] via 192.168.11.2, 00:03:16, Ethernet0/0

O IA    192.168.10.4 [110/20] via 192.168.11.2, 00:03:16, Ethernet0/0

O IA    192.168.10.8 [110/30] via 192.168.11.2, 00:03:16, Ethernet0/0

O IA    192.168.10.12 [110/30] via 192.168.11.2, 00:03:16, Ethernet0/0

O IA    192.168.10.16 [110/30] via 192.168.11.2, 00:03:16, Ethernet0/0

     192.168.11.0/30 is subnetted, 1 subnets

C       192.168.11.0 is directly connected, Ethernet0/0

     192.168.0.0/32 is subnetted, 5 subnets

C       192.168.0.1 is directly connected, Loopback0

O IA    192.168.0.2 [110/11] via 192.168.11.2, 00:03:17, Ethernet0/0

O IA    192.168.0.3 [110/21] via 192.168.11.2, 00:03:17, Ethernet0/0

O IA    192.168.0.4 [110/21] via 192.168.11.2, 00:03:17, Ethernet0/0

O IA    192.168.0.5 [110/31] via 192.168.11.2, 00:03:19, Ethernet0/0



R2#show ip route

Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP

       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area

       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2

       E1 - OSPF external type 1, E2 - OSPF external type 2

       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2

       ia - IS-IS inter area, * - candidate default, U - per-user static route

       o - ODR, P - periodic downloaded static route



Gateway of last resort is not set



     192.168.10.0/30 is subnetted, 5 subnets

C       192.168.10.0 is directly connected, Ethernet0/1

C       192.168.10.4 is directly connected, Ethernet0/2

O       192.168.10.8 [110/20] via 192.168.10.6, 00:03:57, Ethernet0/2

O       192.168.10.12 [110/20] via 192.168.10.6, 00:03:57, Ethernet0/2

                      [110/20] via 192.168.10.2, 00:03:57, Ethernet0/1

O       192.168.10.16 [110/20] via 192.168.10.2, 00:03:57, Ethernet0/1

     192.168.11.0/30 is subnetted, 1 subnets

C       192.168.11.0 is directly connected, Ethernet0/0

     192.168.0.0/32 is subnetted, 5 subnets

O       192.168.0.1 [110/11] via 192.168.11.1, 00:03:19, Ethernet0/0

C       192.168.0.2 is directly connected, Loopback0

O       192.168.0.3 [110/11] via 192.168.10.6, 00:03:59, Ethernet0/2

O       192.168.0.4 [110/11] via 192.168.10.2, 00:04:00, Ethernet0/1

O       192.168.0.5 [110/21] via 192.168.10.6, 00:04:01, Ethernet0/2

                    [110/21] via 192.168.10.2, 00:04:01, Ethernet0/1



R3#show ip route

Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP

       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area

       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2

       E1 - OSPF external type 1, E2 - OSPF external type 2

       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2

       ia - IS-IS inter area, * - candidate default, U - per-user static route

       o - ODR, P - periodic downloaded static route



Gateway of last resort is not set



     192.168.10.0/30 is subnetted, 5 subnets

O       192.168.10.0 [110/20] via 192.168.10.14, 00:03:55, Ethernet0/3

                     [110/20] via 192.168.10.5, 00:03:55, Ethernet0/2

C       192.168.10.4 is directly connected, Ethernet0/2

C       192.168.10.8 is directly connected, Ethernet0/0

C       192.168.10.12 is directly connected, Ethernet0/3

O       192.168.10.16 [110/20] via 192.168.10.14, 00:03:55, Ethernet0/3

                      [110/20] via 192.168.10.10, 00:03:55, Ethernet0/0

     192.168.11.0/30 is subnetted, 1 subnets

O IA    192.168.11.0 [110/20] via 192.168.10.5, 00:03:57, Ethernet0/2

     192.168.0.0/32 is subnetted, 5 subnets

O IA    192.168.0.1 [110/21] via 192.168.10.5, 00:03:24, Ethernet0/2

O       192.168.0.2 [110/11] via 192.168.10.5, 00:03:57, Ethernet0/2

C       192.168.0.3 is directly connected, Loopback0

O       192.168.0.4 [110/11] via 192.168.10.14, 00:03:58, Ethernet0/3

O       192.168.0.5 [110/11] via 192.168.10.10, 00:03:58, Ethernet0/0



R4#show ip route

Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP

       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area

       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2

       E1 - OSPF external type 1, E2 - OSPF external type 2

       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2

       ia - IS-IS inter area, * - candidate default, U - per-user static route

       o - ODR, P - periodic downloaded static route



Gateway of last resort is not set



     192.168.10.0/30 is subnetted, 5 subnets

C       192.168.10.0 is directly connected, Ethernet0/1

O       192.168.10.4 [110/20] via 192.168.10.13, 00:04:00, Ethernet0/3

                     [110/20] via 192.168.10.1, 00:04:00, Ethernet0/1

O       192.168.10.8 [110/20] via 192.168.10.18, 00:04:00, Ethernet0/2

                     [110/20] via 192.168.10.13, 00:04:00, Ethernet0/3

C       192.168.10.12 is directly connected, Ethernet0/3

C       192.168.10.16 is directly connected, Ethernet0/2

     192.168.11.0/30 is subnetted, 1 subnets

O IA    192.168.11.0 [110/20] via 192.168.10.1, 00:04:02, Ethernet0/1

     192.168.0.0/32 is subnetted, 5 subnets

O IA    192.168.0.1 [110/21] via 192.168.10.1, 00:03:29, Ethernet0/1

O       192.168.0.2 [110/11] via 192.168.10.1, 00:04:02, Ethernet0/1

O       192.168.0.3 [110/11] via 192.168.10.13, 00:04:03, Ethernet0/3

C       192.168.0.4 is directly connected, Loopback0

O       192.168.0.5 [110/11] via 192.168.10.18, 00:04:03, Ethernet0/2



R5#show ip route

Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP

       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area

       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2

       E1 - OSPF external type 1, E2 - OSPF external type 2

       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2

       ia - IS-IS inter area, * - candidate default, U - per-user static route

       o - ODR, P - periodic downloaded static route



Gateway of last resort is not set



     192.168.10.0/30 is subnetted, 5 subnets

O       192.168.10.0 [110/20] via 192.168.10.17, 00:04:05, Ethernet0/2

O       192.168.10.4 [110/20] via 192.168.10.9, 00:04:05, Ethernet0/0

C       192.168.10.8 is directly connected, Ethernet0/0

O       192.168.10.12 [110/20] via 192.168.10.17, 00:04:05, Ethernet0/2

                      [110/20] via 192.168.10.9, 00:04:05, Ethernet0/0

C       192.168.10.16 is directly connected, Ethernet0/2

     192.168.11.0/30 is subnetted, 1 subnets

O IA    192.168.11.0 [110/30] via 192.168.10.17, 00:04:06, Ethernet0/2

                     [110/30] via 192.168.10.9, 00:04:06, Ethernet0/0

     192.168.0.0/32 is subnetted, 5 subnets

O IA    192.168.0.1 [110/31] via 192.168.10.17, 00:03:34, Ethernet0/2

                    [110/31] via 192.168.10.9, 00:03:34, Ethernet0/0

O       192.168.0.2 [110/21] via 192.168.10.17, 00:04:07, Ethernet0/2

                    [110/21] via 192.168.10.9, 00:04:07, Ethernet0/0

O       192.168.0.3 [110/11] via 192.168.10.9, 00:04:08, Ethernet0/0

O       192.168.0.4 [110/11] via 192.168.10.17, 00:04:08, Ethernet0/2

C       192.168.0.5 is directly connected, Loopback0
```


5
```
R4#ping 192.168.11.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.11.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 40/131/356 ms
R4#
```
```

R4#traceroute 192.168.11.1

Type escape sequence to abort.
Tracing the route to 192.168.11.1

  1 192.168.10.1 28 msec 12 msec 20 msec
  2 192.168.11.1 48 msec 68 msec 52 msec
R4#
```

dalej
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

     192.168.10.0/30 is subnetted, 5 subnets
O IA    192.168.10.0 [110/20] via 192.168.11.2, 00:08:35, Ethernet0/0
O IA    192.168.10.4 [110/20] via 192.168.11.2, 00:08:35, Ethernet0/0
O IA    192.168.10.8 [110/30] via 192.168.11.2, 00:08:35, Ethernet0/0
O IA    192.168.10.12 [110/30] via 192.168.11.2, 00:08:35, Ethernet0/0
O IA    192.168.10.16 [110/30] via 192.168.11.2, 00:08:35, Ethernet0/0
     192.168.11.0/30 is subnetted, 1 subnets
C       192.168.11.0 is directly connected, Ethernet0/0
     192.168.0.0/32 is subnetted, 5 subnets
C       192.168.0.1 is directly connected, Loopback0
O IA    192.168.0.2 [110/11] via 192.168.11.2, 00:08:37, Ethernet0/0
O IA    192.168.0.3 [110/21] via 192.168.11.2, 00:08:37, Ethernet0/0
O IA    192.168.0.4 [110/21] via 192.168.11.2, 00:08:37, Ethernet0/0
O IA    192.168.0.5 [110/31] via 192.168.11.2, 00:08:38, Ethernet0/0
R1#
```

```

R2#show interface Ethernet0/1 | include BW
  MTU 1500 bytes, BW 10000 Kbit/sec, DLY 1000 usec,
R2#show ip ospf interface Ethernet0/1 | include Cost
  Process ID 1, Router ID 192.168.0.2, Network Type POINT_TO_POINT, Cost: 10
R2#
```

```

R2(config-if)#do show ip ospf interface Ethernet0/1 | include Cost
  Process ID 1, Router ID 192.168.0.2, Network Type POINT_TO_POINT, Cost: 100
R2(config-if)#

```


```

R4#ping 192.168.11.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.11.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 56/65/76 ms
R4#traceroute 192.168.11.1

Type escape sequence to abort.
Tracing the route to 192.168.11.1

  1 192.168.10.13 56 msec 12 msec 28 msec
  2 192.168.10.5 48 msec 48 msec 48 msec
  3 192.168.11.1 60 msec 56 msec 76 msec
R4#
```

## last

show ip route
```

R1(config-router)#do show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

R    192.168.10.0/24 [120/2] via 192.168.11.2, 00:00:06, Ethernet0/0
     192.168.11.0/30 is subnetted, 1 subnets
C       192.168.11.0 is directly connected, Ethernet0/0
     192.168.0.0/24 is variably subnetted, 2 subnets, 2 masks
R       192.168.0.0/24 [120/2] via 192.168.11.2, 00:00:06, Ethernet0/0
C       192.168.0.1/32 is directly connected, Loopback0
R1(config-router)#
R5#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     192.168.10.0/30 is subnetted, 5 subnets
O       192.168.10.0 [110/110] via 192.168.10.17, 00:01:00, Ethernet0/2
O       192.168.10.4 [110/20] via 192.168.10.9, 00:01:00, Ethernet0/0
C       192.168.10.8 is directly connected, Ethernet0/0
O       192.168.10.12 [110/20] via 192.168.10.17, 00:01:00, Ethernet0/2
                      [110/20] via 192.168.10.9, 00:01:00, Ethernet0/0
C       192.168.10.16 is directly connected, Ethernet0/2
     192.168.11.0/30 is subnetted, 1 subnets
O E2    192.168.11.0 [110/100] via 192.168.10.9, 00:00:43, Ethernet0/0
     192.168.0.0/24 is variably subnetted, 5 subnets, 2 masks
O E2    192.168.0.0/24 [110/100] via 192.168.10.9, 00:00:42, Ethernet0/0
O       192.168.0.2/32 [110/21] via 192.168.10.9, 00:01:01, Ethernet0/0
O       192.168.0.3/32 [110/11] via 192.168.10.9, 00:01:01, Ethernet0/0
O       192.168.0.4/32 [110/11] via 192.168.10.17, 00:01:08, Ethernet0/2
C       192.168.0.5/32 is directly connected, Loopback0
```

```
R5#show ip ospf database

            OSPF Router with ID (192.168.0.5) (Process ID 1)

                Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
192.168.0.1     192.168.0.1     2018        0x80000003 0x001ECD 2
192.168.0.2     192.168.0.2     76          0x8000000C 0x00B865 5
192.168.0.3     192.168.0.3     1520        0x80000008 0x008E1B 6
192.168.0.4     192.168.0.4     383         0x80000009 0x00E205 6
192.168.0.5     192.168.0.5     1477        0x80000007 0x00BAEB 5

                Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
192.168.10.14   192.168.0.4     33          0x80000002 0x00C7AB

                Type-5 AS External Link States

Link ID         ADV Router      Age         Seq#       Checksum Tag
192.168.0.0     192.168.0.2     54          0x80000001 0x00413A 0
192.168.11.0    192.168.0.2     54          0x80000001 0x00B5BD 0
R5#show ip ospf database external

            OSPF Router with ID (192.168.0.5) (Process ID 1)

                Type-5 AS External Link States

  Routing Bit Set on this LSA
  LS age: 60
  Options: (No TOS-capability, DC)
  LS Type: AS External Link
  Link State ID: 192.168.0.0 (External Network Number )
  Advertising Router: 192.168.0.2
  LS Seq Number: 80000001
  Checksum: 0x413A
  Length: 36
  Network Mask: /24
        Metric Type: 2 (Larger than any link state path)
        TOS: 0
        Metric: 100
        Forward Address: 0.0.0.0
        External Route Tag: 0

  Routing Bit Set on this LSA
  LS age: 60
  Options: (No TOS-capability, DC)
  LS Type: AS External Link
  Link State ID: 192.168.11.0 (External Network Number )
  Advertising Router: 192.168.0.2
  LS Seq Number: 80000001
  Checksum: 0xB5BD
  Length: 36
  Network Mask: /30
        Metric Type: 2 (Larger than any link state path)
        TOS: 0
        Metric: 100
        Forward Address: 0.0.0.0
        External Route Tag: 0

R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#
R5#show ip ospf database router

            OSPF Router with ID (192.168.0.5) (Process ID 1)

                Router Link States (Area 0)

  Adv Router is not-reachable
  LS age: 2035
  Options: (No TOS-capability, DC)
  LS Type: Router Links
  Link State ID: 192.168.0.1
  Advertising Router: 192.168.0.1
  LS Seq Number: 80000003
  Checksum: 0x1ECD
  Length: 48
  Number of Links: 2

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.0.1
     (Link Data) Network Mask: 255.255.255.255
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

    Link connected to: a Transit Network
     (Link ID) Designated Router address: 192.168.11.2
     (Link Data) Router Interface address: 192.168.11.1
      Number of TOS metrics: 0
       TOS 0 Metrics: 10


  Routing Bit Set on this LSA
  LS age: 96
  Options: (No TOS-capability, DC)
  LS Type: Router Links
  Link State ID: 192.168.0.2
  Advertising Router: 192.168.0.2
  LS Seq Number: 8000000C
  Checksum: 0xB865
  Length: 84
  AS Boundary Router
  Number of Links: 5

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.0.2
     (Link Data) Network Mask: 255.255.255.255
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 192.168.0.3
     (Link Data) Router Interface address: 192.168.10.5
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.10.4
     (Link Data) Network Mask: 255.255.255.252
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 192.168.0.4
     (Link Data) Router Interface address: 192.168.10.1
      Number of TOS metrics: 0
       TOS 0 Metrics: 100

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.10.0
     (Link Data) Network Mask: 255.255.255.252
      Number of TOS metrics: 0
       TOS 0 Metrics: 100


  LS age: 1549
  Options: (No TOS-capability, DC)
  LS Type: Router Links
  Link State ID: 192.168.0.3
  Advertising Router: 192.168.0.3
  LS Seq Number: 80000008
  Checksum: 0x8E1B
  Length: 96
  Number of Links: 6

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.0.3
     (Link Data) Network Mask: 255.255.255.255
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

    Link connected to: a Transit Network
     (Link ID) Designated Router address: 192.168.10.14
     (Link Data) Router Interface address: 192.168.10.13
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 192.168.0.5
     (Link Data) Router Interface address: 192.168.10.9
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.10.8
     (Link Data) Network Mask: 255.255.255.252
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 192.168.0.2
     (Link Data) Router Interface address: 192.168.10.6
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.10.4
     (Link Data) Network Mask: 255.255.255.252
      Number of TOS metrics: 0
       TOS 0 Metrics: 10


  LS age: 416
  Options: (No TOS-capability, DC)
  LS Type: Router Links
  Link State ID: 192.168.0.4
  Advertising Router: 192.168.0.4
  LS Seq Number: 80000009
  Checksum: 0xE205
  Length: 96
  Number of Links: 6

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.0.4
     (Link Data) Network Mask: 255.255.255.255
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 192.168.0.5
     (Link Data) Router Interface address: 192.168.10.17
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.10.16
     (Link Data) Network Mask: 255.255.255.252
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: a Transit Network
     (Link ID) Designated Router address: 192.168.10.14
     (Link Data) Router Interface address: 192.168.10.14
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 192.168.0.2
     (Link Data) Router Interface address: 192.168.10.2
      Number of TOS metrics: 0
       TOS 0 Metrics: 100

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.10.0
     (Link Data) Network Mask: 255.255.255.252
      Number of TOS metrics: 0
       TOS 0 Metrics: 100


  LS age: 1515
  Options: (No TOS-capability, DC)
  LS Type: Router Links
  Link State ID: 192.168.0.5
  Advertising Router: 192.168.0.5
  LS Seq Number: 80000007
  Checksum: 0xBAEB
  Length: 84
  Number of Links: 5

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.0.5
     (Link Data) Network Mask: 255.255.255.255
      Number of TOS metrics: 0
       TOS 0 Metrics: 1

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 192.168.0.4
     (Link Data) Router Interface address: 192.168.10.18
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.10.16
     (Link Data) Network Mask: 255.255.255.252
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: another Router (point-to-point)
     (Link ID) Neighboring Router ID: 192.168.0.3
     (Link Data) Router Interface address: 192.168.10.10
      Number of TOS metrics: 0
       TOS 0 Metrics: 10

    Link connected to: a Stub Network
     (Link ID) Network/subnet number: 192.168.10.8
     (Link Data) Network Mask: 255.255.255.252
      Number of TOS metrics: 0
       TOS 0 Metrics: 10
```
