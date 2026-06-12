# ⚙️ Device Configuration

## 🟢 R1-SOHO

### Basic Configuration

```cisco
enable
configure terminal
hostname R1-SOHO
```

### Configure LAN Interface

```cisco
interface GigabitEthernet0/0/0
 ip address 192.168.1.1 255.255.255.0
 ip nat inside
 no shutdown
```

**Purpose**

* Default Gateway สำหรับอุปกรณ์ภายในองค์กร
* กำหนดให้เป็น NAT Inside Interface

---

### Configure WAN Interface

```cisco
interface GigabitEthernet0/0/1
 ip address 203.0.113.1 255.255.255.252
 ip nat outside
 no shutdown
```

**Purpose**

* เชื่อมต่อไปยัง ISP Router
* กำหนดให้เป็น NAT Outside Interface

---

### Configure Default Route

```cisco
ip route 0.0.0.0 0.0.0.0 203.0.113.2
```

**Purpose**

* กำหนดเส้นทางออกสู่เครือข่ายภายนอกทั้งหมดผ่าน ISP Router

---

### Configure DHCP Server

```cisco
ip dhcp excluded-address 192.168.1.1 192.168.1.9

ip dhcp pool SOHO_POOL
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.1
 dns-server 8.8.8.8
```

**Purpose**

* สำรอง IP Address ช่วง 192.168.1.1 - 192.168.1.9 ไว้สำหรับอุปกรณ์เครือข่าย
* แจกจ่าย IP Address ให้กับเครื่องลูกข่ายโดยอัตโนมัติ
* กำหนด Gateway และ DNS Server ให้ผู้ใช้งาน

---

### Configure NAT Overload (PAT)

```cisco
access-list 1 permit 192.168.1.0 0.0.0.255

ip nat inside source list 1 interface GigabitEthernet0/0/1 overload
```

**Purpose**

* อนุญาตให้เครือข่าย 192.168.1.0/24 ใช้งาน NAT
* แปลง Private IP หลายเครื่องให้ออกอินเทอร์เน็ตผ่าน Public IP เดียว
* ใช้ Port Translation (PAT) เพื่อรองรับการเชื่อมต่อพร้อมกันหลายอุปกรณ์

---

### Save Configuration

```cisco
end
write memory
```

---

## 🌐 ISP-Router

### Basic Configuration

```cisco
enable
configure terminal
hostname ISP-Router
```

### Configure Customer Link

```cisco
interface GigabitEthernet0/0/0
 ip address 203.0.113.2 255.255.255.252
 no shutdown
```

### Configure Internet Segment

```cisco
interface GigabitEthernet0/0/1
 ip address 8.8.8.1 255.255.255.0
 no shutdown
```

### Save Configuration

```cisco
end
write memory
```

**Purpose**

* จำลองผู้ให้บริการอินเทอร์เน็ต (ISP)
* เชื่อมต่อเครือข่าย SOHO เข้ากับเครือข่ายภายนอก
* ทำหน้าที่เป็น Next Hop ของ R1-SOHO

---

## 🖧 SW1

### Basic Configuration

```cisco
enable
configure terminal
hostname SW1
```

### Configure Management Interface

```cisco
interface vlan 1
 ip address 192.168.1.2 255.255.255.0
 no shutdown
```

### Configure Default Gateway

```cisco
ip default-gateway 192.168.1.1
```

**Purpose**

* กำหนด Management IP สำหรับบริหารจัดการสวิตช์
* กำหนดเส้นทางออกจากสวิตช์ไปยัง Router

### Save Configuration

```cisco
end
write memory
```

---

# 🔍 Verification Commands

### DHCP

```cisco
show ip dhcp binding
```

ตรวจสอบรายการ IP Address ที่ถูกแจกให้กับ Client

### NAT

```cisco
show ip nat translations
```

ตรวจสอบการแปลง Private IP เป็น Public IP

### Routing

```cisco
show ip route
```

ตรวจสอบ Routing Table ของ Router

### Interface Status

```cisco
show ip interface brief
```

ตรวจสอบสถานะของทุก Interface
