# ⚙️ Device Configuration

---

# 🟢 HQ Router (HQ_Router)

## หน้าที่

* Gateway หลักของสำนักงานใหญ่ (HQ)
* แจก IP Address ผ่าน DHCP พร้อม Option 43 สำหรับ WLC
* สร้าง GRE Tunnel และเข้ารหัสด้วย IPsec VPN
* แลกเปลี่ยนเส้นทาง OSPF กับ Branch Router ผ่าน Tunnel

## Basic Configuration

```cisco
enable
configure terminal
hostname HQ_Router
```

## Configure Interfaces

```cisco
interface GigabitEthernet0/0
 description WAN_to_ISP
 ip address 200.0.0.2 255.255.255.252
 no shutdown
exit

interface GigabitEthernet0/1
 description LAN_HQ
 ip address 10.0.10.1 255.255.255.0
 no shutdown
exit
```

## Configure Default Route

```cisco
ip route 0.0.0.0 0.0.0.0 200.0.0.1
```

## Configure DHCP Server

```cisco
ip dhcp excluded-address 10.0.10.1 10.0.10.20

ip dhcp pool HQ_LAN
 network 10.0.10.0 255.255.255.0
 default-router 10.0.10.1
 dns-server 8.8.8.8
 option 43 ip 10.0.10.20
exit
```

## Configure IPsec VPN (Phase 1 & Phase 2)

```cisco
crypto isakmp policy 10
 encr aes
 hash sha
 authentication pre-share
 group 2
exit

crypto isakmp key cisco123 address 200.0.0.6

crypto ipsec transform-set TS esp-aes esp-sha-hmac

crypto map VPNMAP 10 ipsec-isakmp
 set peer 200.0.0.6
 set transform-set TS
 match address 100
exit
```

## Configure GRE Tunnel

```cisco
interface Tunnel0
 ip address 172.16.0.1 255.255.255.252
 tunnel source GigabitEthernet0/0
 tunnel destination 200.0.0.6
exit
```

## Apply Crypto Map & Configure ACL

```cisco
interface GigabitEthernet0/0
 crypto map VPNMAP
exit

access-list 100 permit gre host 200.0.0.2 host 200.0.0.6
```

## Configure OSPF

```cisco
router ospf 1
 network 10.0.10.0 0.0.0.255 area 0
 network 172.16.0.0 0.0.0.3 area 0
exit
```

## Save Configuration

```cisco
end
write memory
```

---

# 🟢 Branch Router (Branch_Router)

## หน้าที่

* Gateway หลักของสาขาย่อย (Branch)
* แจก IP Address ให้ผู้ใช้งานภายในสาขา
* สร้าง GRE Tunnel และเข้ารหัสด้วย IPsec VPN กลับไปยัง HQ
* แลกเปลี่ยนเส้นทาง OSPF ผ่าน VPN Tunnel

## Basic Configuration

```cisco
enable
configure terminal
hostname Branch_Router
```

## Configure Interfaces

```cisco
interface GigabitEthernet0/0
 description WAN_to_ISP
 ip address 200.0.0.6 255.255.255.252
 no shutdown
exit

interface GigabitEthernet0/1
 description LAN_Branch
 ip address 192.168.30.1 255.255.255.0
 no shutdown
exit
```

## Configure Default Route

```cisco
ip route 0.0.0.0 0.0.0.0 200.0.0.5
```

## Configure DHCP Server

```cisco
ip dhcp excluded-address 192.168.30.1 192.168.30.10

ip dhcp pool BRANCH_LAN
 network 192.168.30.0 255.255.255.0
 default-router 192.168.30.1
 dns-server 8.8.8.8
exit
```

## Configure IPsec VPN (Phase 1 & Phase 2)

```cisco
crypto isakmp policy 10
 encr aes
 hash sha
 authentication pre-share
 group 2
exit

crypto isakmp key cisco123 address 200.0.0.2

crypto ipsec transform-set TS esp-aes esp-sha-hmac

crypto map VPNMAP 10 ipsec-isakmp
 set peer 200.0.0.2
 set transform-set TS
 match address 100
exit
```

## Configure GRE Tunnel

```cisco
interface Tunnel0
 ip address 172.16.0.2 255.255.255.252
 tunnel source GigabitEthernet0/0
 tunnel destination 200.0.0.2
exit
```

## Apply Crypto Map & Configure ACL

```cisco
interface GigabitEthernet0/0
 crypto map VPNMAP
exit

access-list 100 permit gre host 200.0.0.6 host 200.0.0.2
```

## Configure OSPF

```cisco
router ospf 1
 network 192.168.30.0 0.0.0.255 area 0
 network 172.16.0.0 0.0.0.3 area 0
exit
```

## Save Configuration

```cisco
end
write memory
```

---

# 🌐 ISP Router (ISP_Router)

## หน้าที่

* จำลองผู้ให้บริการอินเทอร์เน็ต (ISP)
* ทำหน้าที่เป็น Transit Network ระหว่าง HQ และ Branch
* ส่งผ่านทราฟฟิก VPN ระหว่างทั้งสองไซต์

## Basic Configuration

```cisco
enable
configure terminal
hostname ISP_Router
```

## Configure Interfaces

```cisco
interface GigabitEthernet0/0
 description Link_to_HQ
 ip address 200.0.0.1 255.255.255.252
 no shutdown
exit

interface GigabitEthernet0/1
 description Link_to_Branch
 ip address 200.0.0.5 255.255.255.252
 no shutdown
exit
```

## Save Configuration

```cisco
end
write memory
```

---

# 🖧 HQ Switch (HQ_SW)

## หน้าที่

* Access Switch สำหรับอุปกรณ์ภายในสำนักงานใหญ่
* เชื่อมต่อ PC, Laptop, Printer และ Access Point

## Configuration

```cisco
enable
configure terminal
hostname HQ_SW

end
write memory
```

---

# 🖧 Branch Switch (Branch_SW)

## หน้าที่

* Access Switch สำหรับอุปกรณ์ภายในสาขา
* เชื่อมต่อผู้ใช้งานและอุปกรณ์ภายในสาขา

## Configuration

```cisco
enable
configure terminal
hostname Branch_SW

end
write memory
```

---

# 🔍 Verification Commands

## Verify ISAKMP Phase 1

```cisco
show crypto isakmp sa
```

## Verify IPsec Phase 2

```cisco
show crypto ipsec sa
```

## Verify GRE Tunnel Status

```cisco
show interfaces tunnel 0
```

## Verify OSPF Neighbors

```cisco
show ip ospf neighbor
```

## Verify OSPF Routes

```cisco
show ip route ospf
```

## Verify Routing Table

```cisco
show ip route
```

## Verify DHCP Bindings

```cisco
show ip dhcp binding
```

## Verify Interface Status

```cisco
show ip interface brief
```

## Verify Running Configuration

```cisco
show running-config
```
