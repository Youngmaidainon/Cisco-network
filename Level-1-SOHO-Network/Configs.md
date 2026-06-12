## R1-SOHO

enable
configure terminal
hostname R1-SOHO

 --- คอนฟิกพอร์ต LAN & WAN ---
interface GigabitEthernet0/0/0
    ip address 192.168.1.1 255.255.255.0
    ip nat inside -- พอร์ตที่หันหน้าเข้าหาเครือข่ายภายในบริษัท --
    no shutdown

interface GigabitEthernet0/0/1
    ip address 203.0.113.1 255.255.255.252
    ip nat outside -- พอร์ตขา WAN ที่ต่อเชื่อมออกไปทางฝั่งผู้ให้บริการอินเทอร์เน็ตภายนอก --
    no shutdown

 --- ตั้งค่าเส้นทางออกอินเทอร์เน็ต (Static Route) ---
ip route 0.0.0.0 0.0.0.0 203.0.113.2

 --- คอนฟิก DHCP Server แจกจ่ายไอพีภายในองค์กร ---
ip dhcp excluded-address 192.168.1.1 192.168.1.9 -- ip dhcp excluded-address [ห้ามแจก IP เริ่มต้น] [จนถึง IP สิ้นสุด] --
ip dhcp pool SOHO_POOL -- ip dhcp pool [ชื่อกลุ่ม] --
    network 192.168.1.0 255.255.255.0
    default-router 192.168.1.1
    dns-server 8.8.8.8

 --- ทำระบบ NAT (PAT) เพื่อแปลงไอพีภายในให้ออกเน็ตได้ ---
access-list 1 permit 192.168.1.0 0.0.0.255 
-- สร้างกฎ Standard Access List เบอร์ 1 เพื่อระบุว่า หมายเลข IP วงภายในวงไหนบ้างที่มีสิทธิ์ (Permit) ได้รับสิทธิ์ในการแปลงค่าไอพีเพื่อออกไปใช้งานอินเทอร์เน็ตภายนอกองค์กรได้ --

ip nat inside source list 1 interface GigabitEthernet0/0/1 overload 
-- สั่งการให้เราเตอร์แปลงไอพีภายในทั้งหมดที่ระบุไว้ใน Access List เบอร์ 1 ให้มาจับคู่ใช้งาน Public IP ของอินเทอร์เฟซขาออกตัวเดียวกัน โดยอาศัยหมายเลข Port ของระบบช่วยจำแนกแยกแยะ --

end
write memory (จะเซฟหรือไม่ก็ได้ใน cisco packet tracer)


## ISP-Router

enable
configure terminal
hostname ISP-Router

interface GigabitEthernet0/0/0
    ip address 203.0.113.2 255.255.255.252
    no shutdown

interface GigabitEthernet0/0/1
    ip address 8.8.8.1 255.255.255.0
    no shutdown
end
write memory


## SW1

enable
configure terminal
hostname SW1

interface vlan 1
    ip address 192.168.1.2 255.255.255.0
    no shutdown

ip default-gateway 192.168.1.1 -- ip default-gateway [IP ของเราเตอร์] --
end
write memory