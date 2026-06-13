# ⚙️ Device Configuration

---

# 🟢 Core Switch 1 (CSW1-Core) - Active

**หน้าที่**

* Gateway หลักของ VLAN 10 และ VLAN 20
* HSRP Active Router
* Spanning Tree Root Primary
* เชื่อมต่อ OSPF กับ R1-WAN

## Basic Configuration

```cisco
enable
configure terminal
hostname CSW1-Core

ip routing
```

## Create VLAN

```cisco
vlan 10
 name Users

vlan 20
 name Servers
exit
```

## Configure STP Root Primary

```cisco
spanning-tree vlan 10,20 root primary
```

## Configure Trunk Ports

```cisco
interface range GigabitEthernet0/2 , FastEthernet0/1-2
 switchport trunk encapsulation dot1q
 switchport mode trunk
exit
```

## Configure Routed Port to R1-WAN

```cisco
interface GigabitEthernet0/1
 no switchport
 ip address 10.0.11.1 255.255.255.252
 no shutdown
exit
```

## Configure HSRP - VLAN 10

```cisco
interface vlan 10
 ip address 10.1.10.1 255.255.255.0
 standby 10 ip 10.1.10.254
 standby 10 priority 110
 standby 10 preempt
 no shutdown
exit
```

## Configure HSRP - VLAN 20

```cisco
interface vlan 20
 ip address 10.1.20.1 255.255.255.0
 standby 20 ip 10.1.20.254
 standby 20 priority 110
 standby 20 preempt
 no shutdown
exit
```

## Configure OSPF

```cisco
router ospf 1
 network 10.1.10.0 0.0.0.255 area 0
 network 10.1.20.0 0.0.0.255 area 0
 network 10.0.11.0 0.0.0.3 area 0
exit
```

## Save Configuration

```cisco
end
write memory
```

---

# 🟢 Core Switch 2 (CSW2-Core) - Standby

**หน้าที่**

* Gateway สำรอง
* HSRP Standby Router
* Spanning Tree Root Secondary
* เชื่อมต่อ OSPF กับ R2-WAN

## Basic Configuration

```cisco
enable
configure terminal
hostname CSW2-Core

ip routing
```

## Create VLAN

```cisco
vlan 10
 name Users

vlan 20
 name Servers
exit
```

## Configure STP Root Secondary

```cisco
spanning-tree vlan 10,20 root secondary
```

## Configure Trunk Ports

```cisco
interface range GigabitEthernet0/2 , FastEthernet0/1-2
 switchport trunk encapsulation dot1q
 switchport mode trunk
exit
```

## Configure Routed Port to R2-WAN

```cisco
interface GigabitEthernet0/1
 no switchport
 ip address 10.0.22.1 255.255.255.252
 no shutdown
exit
```

## Configure HSRP - VLAN 10

```cisco
interface vlan 10
 ip address 10.1.10.2 255.255.255.0
 standby 10 ip 10.1.10.254
 standby 10 preempt
 no shutdown
exit
```

## Configure HSRP - VLAN 20

```cisco
interface vlan 20
 ip address 10.1.20.2 255.255.255.0
 standby 20 ip 10.1.20.254
 standby 20 preempt
 no shutdown
exit
```

## Configure OSPF

```cisco
router ospf 1
 network 10.1.10.0 0.0.0.255 area 0
 network 10.1.20.0 0.0.0.255 area 0
 network 10.0.22.0 0.0.0.3 area 0
exit
```

## Save Configuration

```cisco
end
write memory
```

---

# 🌐 Edge Router 1 (R1-WAN)

**หน้าที่**

* เชื่อมต่อ CSW1-Core ไปยัง ISP1
* กระจาย Default Route เข้าสู่ OSPF

## Configuration

```cisco
enable
configure terminal
hostname R1-WAN

interface GigabitEthernet0/0
 ip address 10.0.11.2 255.255.255.252
 no shutdown
exit

interface GigabitEthernet0/1
 ip address 203.0.113.2 255.255.255.252
 no shutdown
exit

ip route 0.0.0.0 0.0.0.0 203.0.113.1

router ospf 1
 network 10.0.11.0 0.0.0.3 area 0
 default-information originate
exit

end
write memory
```

---

# 🌐 Edge Router 2 (R2-WAN)

**หน้าที่**

* เชื่อมต่อ CSW2-Core ไปยัง ISP2
* เป็นเส้นทางสำรองสำหรับการออกอินเทอร์เน็ต

## Configuration

```cisco
enable
configure terminal
hostname R2-WAN

interface GigabitEthernet0/0
 ip address 10.0.22.2 255.255.255.252
 no shutdown
exit

interface GigabitEthernet0/1
 ip address 198.51.100.2 255.255.255.252
 no shutdown
exit

ip route 0.0.0.0 0.0.0.0 198.51.100.1

router ospf 1
 network 10.0.22.0 0.0.0.3 area 0
 default-information originate
exit

end
write memory
```

---

# 🖧 Access Switch 1 (ASW1-User)

**หน้าที่**

* เชื่อมต่อเครื่องผู้ใช้งาน VLAN 10

## Configuration

```cisco
enable
configure terminal
hostname ASW1-User

vlan 10
 name Users
exit

interface range GigabitEthernet0/1-2
 switchport mode trunk
exit

interface FastEthernet0/1
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
exit

end
write memory
```

---

# 🖧 Access Switch 2 (ASW2-Server)

**หน้าที่**

* เชื่อมต่อ Server VLAN 20

## Configuration

```cisco
enable
configure terminal
hostname ASW2-Server

vlan 20
 name Servers
exit

interface range GigabitEthernet0/1-2
 switchport mode trunk
exit

interface FastEthernet0/1
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
exit

end
write memory
```

---

# 🌍 ISP1

## Configuration

```cisco
enable
configure terminal
hostname ISP1

interface GigabitEthernet0/0
 ip address 203.0.113.1 255.255.255.252
 no shutdown
exit

interface loopback0
 ip address 8.8.8.8 255.255.255.255
exit

ip route 10.0.0.0 255.0.0.0 203.0.113.2

end
write memory
```

---

# 🌍 ISP2

## Configuration

```cisco
enable
configure terminal
hostname ISP2

interface GigabitEthernet0/0
 ip address 198.51.100.1 255.255.255.252
 no shutdown
exit

interface loopback0
 ip address 8.8.8.8 255.255.255.255
exit

ip route 10.0.0.0 255.0.0.0 198.51.100.2

end
write memory
```

---

# 🔍 Verification Commands

## Verify HSRP

```cisco
show standby brief
```

## Verify OSPF Neighbors

```cisco
show ip ospf neighbor
```

## Verify OSPF Routes

```cisco
show ip route ospf
```

## Verify Spanning Tree

```cisco
show spanning-tree
```

## Verify Trunk

```cisco
show interfaces trunk
```

## Verify VLAN

```cisco
show vlan brief
```

## Verify Routing Table

```cisco
show ip route
```

## Verify Interface Status

```cisco
show ip interface brief
```

## Verify Running Configuration

```cisco
show running-config
```
