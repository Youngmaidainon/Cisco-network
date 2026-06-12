# ⚙️ Device Configuration

## 🟢 R1-Core

### Basic Configuration

```cisco
enable
configure terminal
hostname R1-Core
```

### Configure Physical Interface

```cisco
interface GigabitEthernet0/0/0
 no shutdown
```

**Purpose**

- เปิดใช้งานพอร์ตหลักที่เชื่อมต่อกับ Switch
- ใช้งานร่วมกับ Router-on-a-Stick

---

### Configure Router-on-a-Stick

```cisco
interface GigabitEthernet0/0/0.10
 encapsulation dot1Q 10
 ip address 10.0.10.1 255.255.255.0

interface GigabitEthernet0/0/0.20
 encapsulation dot1Q 20
 ip address 10.0.20.1 255.255.255.0

interface GigabitEthernet0/0/0.30
 encapsulation dot1Q 30
 ip address 10.0.30.1 255.255.255.0

interface GigabitEthernet0/0/0.99
 encapsulation dot1Q 99 native
 ip address 10.0.99.1 255.255.255.0
```

**Purpose**

- VLAN 10 (IT) Gateway = 10.0.10.1
- VLAN 20 (HR) Gateway = 10.0.20.1
- VLAN 30 (Finance) Gateway = 10.0.30.1
- VLAN 99 (Management) Gateway = 10.0.99.1
- รองรับ Inter-VLAN Routing ผ่าน Router-on-a-Stick

---

### Save Configuration

```cisco
end
write memory
```

---

# 🖧 SW-Floor1

### Basic Configuration

```cisco
enable
configure terminal
hostname SW-Floor1
```

### Create VLAN Database

```cisco
vlan 10
 name IT

vlan 20
 name HR

vlan 30
 name Finance

vlan 99
 name Management
```

**Purpose**

- สร้าง VLAN สำหรับแต่ละแผนก
- VLAN 99 ใช้สำหรับบริหารจัดการอุปกรณ์

---

### Configure Trunk to Router

```cisco
interface GigabitEthernet0/1
 switchport mode trunk
 switchport trunk native vlan 99
```

**Purpose**

- เชื่อมต่อ R1-Core แบบ Trunk
- อนุญาตให้ VLAN ทุกตัววิ่งผ่านลิงก์เดียว

---

### Configure EtherChannel to SW-Floor2

```cisco
interface range FastEthernet0/23 - 24
 channel-group 1 mode desirable
 switchport mode trunk
 switchport trunk native vlan 99
```

**Purpose**

- รวมลิงก์ 2 เส้นเป็น EtherChannel
- เพิ่ม Bandwidth และ Redundancy

---

### Configure Access Ports

```cisco
interface FastEthernet0/1
 switchport mode access
 switchport access vlan 10
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation shutdown

interface FastEthernet0/2
 switchport mode access
 switchport access vlan 20

interface FastEthernet0/3
 switchport mode access
 switchport access vlan 30
```

**Purpose**

- Fa0/1 → VLAN 10 (IT)
- Fa0/2 → VLAN 20 (HR)
- Fa0/3 → VLAN 30 (Finance)
- เปิด Port Security ที่ Fa0/1 เพื่อป้องกันอุปกรณ์แปลกปลอม

---

### Configure Management Interface

```cisco
interface vlan 99
 ip address 10.0.99.11 255.255.255.0
 no shutdown

ip default-gateway 10.0.99.1
```

**Purpose**

- ใช้บริหารจัดการ SW-Floor1 ผ่าน VLAN 99

---

### Save Configuration

```cisco
end
write memory
```

---

# 🖧 SW-Floor2

### Basic Configuration

```cisco
enable
configure terminal
hostname SW-Floor2
```

### Create VLAN Database

```cisco
vlan 10
 name IT

vlan 20
 name HR

vlan 30
 name Finance

vlan 99
 name Management
```

---

### Configure EtherChannel

```cisco
interface range FastEthernet0/23 - 24
 channel-group 1 mode desirable
 switchport mode trunk
 switchport trunk native vlan 99
```

**Purpose**

- เชื่อมต่อกับ SW-Floor1 ผ่าน EtherChannel

---

### Configure Access Ports

```cisco
interface FastEthernet0/1
 switchport mode access
 switchport access vlan 10

interface FastEthernet0/2
 switchport mode access
 switchport access vlan 20

interface FastEthernet0/3
 switchport mode access
 switchport access vlan 30
```

**Purpose**

- Fa0/1 → VLAN 10 (IT)
- Fa0/2 → VLAN 20 (HR)
- Fa0/3 → VLAN 30 (Finance)

---

### Configure Management Interface

```cisco
interface vlan 99
 ip address 10.0.99.12 255.255.255.0
 no shutdown

ip default-gateway 10.0.99.1
```

**Purpose**

- ใช้บริหารจัดการ SW-Floor2 ผ่าน VLAN 99

---

### Save Configuration

```cisco
end
write memory
```

---

# 🔍 Verification Commands

## Verify VLAN

```cisco
show vlan brief
```

ตรวจสอบ VLAN และพอร์ตสมาชิก

## Verify Trunk

```cisco
show interfaces trunk
```

ตรวจสอบสถานะ Trunk Port

## Verify EtherChannel

```cisco
show etherchannel summary
```

ตรวจสอบสถานะ Port-Channel

## Verify Port Security

```cisco
show port-security
show port-security interface fastEthernet0/1
```

ตรวจสอบสถานะ Port Security และ MAC Address ที่เรียนรู้

## Verify Inter-VLAN Routing

```cisco
show ip interface brief
```

ตรวจสอบ Sub-Interface ของ Router

## Verify MAC Address Table

```cisco
show mac address-table
```

ตรวจสอบการเรียนรู้ MAC Address ของสวิตช์

## Verify Running Configuration

```cisco
show running-config
```

ตรวจสอบการตั้งค่าปัจจุบันทั้งหมดของอุปกรณ์เครือข่าย