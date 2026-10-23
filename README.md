# eigro-ospf-integration-redistribution-architecture
An enterprise-grade routing architecture demonstrating Multi-Area OSPFv2, EIGRP Domain Integration, Mutual Route Redistribution, and ISP Edge Connectivity.

An enterprise-grade routing architecture demonstrating Multi-Area OSPFv2, EIGRP Domain Integration, Mutual Route Redistribution, and ISP Edge Connectivity.🌐 Quick Overview & Network Specs🟢 Architecture & Topology: Enterprise network topology featuring OSPF Area 0 (Backbone), OSPF Area 1 (Internal Subnets), an autonomous EIGRP 100 Domain, and an isolated ISP External Transit Domain (203.0.113.0/30).🔵 Core Routing Protocols:OSPFv2 (Process ID 1): Active across Area 0 (R1, R2, R3) and Area 1 (R3, R4, R5).EIGRP (AS 100): Active on R5 and R6 (advertising subnets 10.0.1.0/24 through 10.0.5.0/24).🟣 Inter-Area Communication (ABR): Router R3 (3.3.3.3) acts as the Area Border Router between Area 0 and Area 1, synthesizing topology details into Type 3 Summary LSAs (O IA).🔴 External Redistribution (ASBRs):Router R1 (1.1.1.1): ASBR redistributing static default routes to the ISP (8.8.8.8) into OSPF via Type 5 External LSAs (O E2).Router R5 (5.5.5.5): Autonomous System Boundary Router performing mutual redistribution between OSPF Area 1 and EIGRP 100.🟡 Layer 2 & Point-to-Point Transit Infrastructure:Multi-access backbone interconnect via Switch0 (123.0.0.0/24) handling DR/BDR election.Point-to-point transit subnets: 23.0.0.0/24 (R2-R3), 34.0.0.0/24 (R3-R4), 45.0.0.0/24 (R4-R5), and 56.0.0.0/24 (R5-R6).🟠 High-Availability & Redundancy: Dual paths within Area 0 (23.0.0.0/24 and 123.0.0.0/24) to eliminate single points of failure across backbone routers.🗺️ Detailed Topology Breakdown [ISP 8.8.8.8] 
       | 203.0.113.0/30
 +-----------+            23.0.0.0/24            +-----------+            34.0.0.0/24            +-----------+            45.0.0.0/24            +-----------+            56.0.0.0/24            +-----------+
 | R1 (ASBR) |===================================| R2 (Core) |===================================| R3 (ABR)  |===================================| R4 (Int)  |===================================| R5 (ASBR) |===================================|  R6 (Edge) |
 |  1.1.1.1  |             (OSPF Area 0)         |  2.2.2.2  |             (OSPF Area 0)         |  3.3.3.3  |             (OSPF Area 1)         |  4.4.4.4  |            (OSPF / EIGRP)         |  5.5.5.5  |             (EIGRP 100)           |  6.6.6.6  |
 +-----------+                                   +-----------+                                   +-----------+                                   +-----------+                                   +-----------+                                   +-----------+
       |                                               |                                               |                                                                                               |                                               |
       +-----------------------------------------------+-----------------------------------------------+                                                                                               +-----------------------------------------------+
                                                       |
                                               [ Switch0: 123.0.0.0/24 ]
📐 Addressing & Router ID AllocationDeviceRouter ID / LoopbackDomain / AreaConnected SubnetsRoleR11.1.1.1OSPF Area 0 / ISP Edge123.0.0.0/24, 203.0.113.0/30ASBR (ISP Default Route Injection)R22.2.2.2OSPF Area 0123.0.0.0/24, 23.0.0.0/24Backbone Core RouterR33.3.3.3OSPF Area 0 & Area 1123.0.0.0/24, 23.0.0.0/24, 34.0.0.0/24Area Border Router (ABR)R44.4.4.4OSPF Area 134.0.0.0/24, 45.0.0.0/24Internal Area 1 RouterR55.5.5.5OSPF Area 1 / EIGRP 10045.0.0.0/24, 56.0.0.0/24ASBR (OSPF <-> EIGRP Redistribution)R66.6.6.6EIGRP 10056.0.0.0/24, 10.0.1.0/24 - 10.0.5.0/24EIGRP Edge Router💡 Key Technical Insights & What This Lab Demonstrates🔍 LSA Hierarchy & Redistribution Analysis: Demonstrates dynamic translation between Type 1 Router LSAs, Type 2 Network LSAs, Type 3 Summary LSAs, and Type 5 External LSAs (O E2) across domain boundaries.⚡ Administrative Distance & Protocol Metric Metrics Handling: Highlights metric conversion during redistribution between bandwidth/delay vector calculations (EIGRP) and link cost metrics (OSPF).🛠️ Real-World Verification Skills: Accompanied by CLI diagnostic validation using:show ip ospf neighborshow ip eigrp neighborsshow ip ospf databaseshow ip routetraceroute ICMP connectivity verification to external networks (8.8.8.8).🛡️ Edge Security & Routing Hygiene: Illustrates route filtering, seed metric assignment during redistribution, and passive interface deployment to prevent unauthorized routing updates.🛠️ CLI Configuration Snippets1. Router R3 (ABR Configuration)R3# configure terminal
R3(config)# router ospf 1
R3(config-router)# router-id 3.3.3.3
R3(config-router)# network 123.0.0.0 0.0.0.255 area 0
R3(config-router)# network 23.0.0.0 0.0.0.255 area 0
R3(config-router)# network 34.0.0.0 0.0.0.255 area 1
2. Router R5 (ASBR Mutual Redistribution)R5# configure terminal
! OSPF Configuration with EIGRP Redistribution
R5(config)# router ospf 1
R5(config-router)# router-id 5.5.5.5
R5(config-router)# network 45.0.0.0 0.0.0.255 area 1
R5(config-router)# redistribute eigrp 100 subnets metric-type 2

! EIGRP Configuration with OSPF Redistribution
R5(config)# router eigrp 100
R5(config-router)# autonomous-system 100
R5(config-router)# network 56.0.0.0 0.0.0.255
R5(config-router)# auto-summary
R5(config-router)# redistribute ospf 1 metric 10000 100 255 1 1500
