| ชื่ออุปกรณ์ | พอร์ต / อินเทอร์เฟซ | IP Address | Subnet Mask | Default Gateway | หมายเหตุ / หน้าที่ |
|------------|---------------------|------------|-------------|------------------|--------------------|
| **CSW1-Core** | Interface VLAN 10 | 10.1.10.1 | 255.255.255.0 | N/A | SVI VLAN 10 (Active HSRP) |
| **CSW2-Core** | Interface VLAN 10 | 10.1.10.2 | 255.255.255.0 | N/A | SVI VLAN 10 (Standby HSRP) |
| **HSRP VIP** | VLAN 10 | 10.1.10.254 | 255.255.255.0 | N/A | Virtual Gateway สำหรับ VLAN 10 |
| **PC-User1** | FastEthernet0 | 10.1.10.11 | 255.255.255.0 | 10.1.10.254 | เครื่องผู้ใช้งาน VLAN 10 |
| **PC-User2** | FastEthernet0 | 10.1.10.12 | 255.255.255.0 | 10.1.10.254 | เครื่องผู้ใช้งาน VLAN 10 |
| **CSW1-Core** | Interface VLAN 20 | 10.1.20.1 | 255.255.255.0 | N/A | SVI VLAN 20 (Active HSRP) |
| **CSW2-Core** | Interface VLAN 20 | 10.1.20.2 | 255.255.255.0 | N/A | SVI VLAN 20 (Standby HSRP) |
| **HSRP VIP** | VLAN 20 | 10.1.20.254 | 255.255.255.0 | N/A | Virtual Gateway สำหรับ VLAN 20 |
| **Server-Web** | FastEthernet0 | 10.1.20.100 | 255.255.255.0 | 10.1.20.254 | Web Server |
| **Server-DNS** | FastEthernet0 | 10.1.20.101 | 255.255.255.0 | 10.1.20.254 | DNS Server |
| **Server-DHCP** | FastEthernet0 | 10.1.20.102 | 255.255.255.0 | 10.1.20.254 | DHCP Server |
| **CSW1-Core** | GigabitEthernet0/1 | 10.0.11.1 | 255.255.255.252 | N/A | Routed Port เชื่อมต่อ R1-WAN |
| **R1-WAN** | GigabitEthernet0/0 | 10.0.11.2 | 255.255.255.252 | N/A | เชื่อมต่อ CSW1-Core |
| **CSW2-Core** | GigabitEthernet0/1 | 10.0.22.1 | 255.255.255.252 | N/A | Routed Port เชื่อมต่อ R2-WAN |
| **R2-WAN** | GigabitEthernet0/0 | 10.0.22.2 | 255.255.255.252 | N/A | เชื่อมต่อ CSW2-Core |
| **R1-WAN** | GigabitEthernet0/1 | 203.0.113.2 | 255.255.255.252 | N/A | WAN Link ไป ISP1 |
| **ISP1** | GigabitEthernet0/0 | 203.0.113.1 | 255.255.255.252 | N/A | จำลองผู้ให้บริการอินเทอร์เน็ต |
| **R2-WAN** | GigabitEthernet0/1 | 198.51.100.2 | 255.255.255.252 | N/A | WAN Link ไป ISP2 |
| **ISP2** | GigabitEthernet0/0 | 198.51.100.1 | 255.255.255.252 | N/A | จำลองผู้ให้บริการอินเทอร์เน็ต |
| **ISP1** | Loopback0 | 8.8.8.8 | 255.255.255.255 | N/A | จำลอง Internet Service |
| **ISP2** | Loopback0 | 8.8.8.8 | 255.255.255.255 | N/A | จำลอง Internet Service |