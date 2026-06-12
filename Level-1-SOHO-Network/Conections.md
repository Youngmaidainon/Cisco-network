| ชื่ออุปกรณ์            | พอร์ต (Interface)      | IP Address  | Subnet Mask     | Default Gateway | หมายเหตุ / หน้าที่                            |
| ------------------- | --------------------- | ----------- | --------------- | --------------- | --------------------------------------------- |
| **R1-SOHO**         | GigabitEthernet 0/0/0 | 192.168.1.1 | 255.255.255.0   | N/A             | ขา LAN (Gateway ของออฟฟิศ)                    |
| **R1-SOHO**         | GigabitEthernet 0/0/1 | 203.0.113.1 | 255.255.255.252 | N/A             | ขา WAN (ต่อออกไปหา ISP)                       |
| **ISP-Router**      | GigabitEthernet 0/0/0 | 203.0.113.2 | 255.255.255.252 | N/A             | ขาที่เชื่อมกลับมาหา R1-SOHO                   |
| **ISP-Router**      | GigabitEthernet 0/0/1 | 8.8.8.1     | 255.255.255.0   | N/A             | ขาที่ต่อเชื่อมกับเซิร์ฟเวอร์อินเทอร์เน็ต      |
| **Internet-Server** | FastEthernet 0        | 8.8.8.8     | 255.255.255.0   | 8.8.8.1         | จำลองเป็น Google Public DNS                   |
| **SW1**             | Interface VLAN 1      | 192.168.1.2 | 255.255.255.0   | 192.168.1.1     | IP สำหรับรีโมทจัดการสวิตช์                    |
| **Wi-Fi-Router**    | Internet Port (WAN)   | 192.168.1.5 | 255.255.255.0   | 192.168.1.1     | รับเน็ตมาจาก SW1 (ตั้งค่าเป็น Static)         |
| **Wi-Fi-Router**    | LAN Port / Wireless   | 192.168.0.1 | 255.255.255.0   | N/A             | IP ฝั่งภายในวง Wi-Fi (ค่าเริ่มต้น)            |
| **PC-Admin**        | FastEthernet 0        | DHCP        | 255.255.255.0   | 192.168.1.1     | จะได้รับ IP ช่วง 192.168.1.10 - 192.168.1.254 |
| **Printer-LAN**     | FastEthernet 0        | 192.168.1.9 | 255.255.255.0   | 192.168.1.1     | ฟิกซ์ IP ไว้เพื่อให้เรียกใช้งานง่าย           |
| **Laptop-Wireless** | Wireless 0            | DHCP        | 255.255.255.0   | 192.168.0.1     | ได้รับ IP อัตโนมัติจากวง Wi-Fi-Router         |

graph LR
    PC[PC-Admin<br>DHCP]
    PRN[Printer-LAN<br>192.168.1.9]
    SW[SW1<br>192.168.1.2]
    R1[R1-SOHO<br>LAN: 192.168.1.1<br>WAN: 203.0.113.1]
    ISP[ISP-Router<br>203.0.113.2<br>8.8.8.1]
    DNS[Internet-Server<br>8.8.8.8]
    WIFI[Wi-Fi Router<br>WAN: 192.168.1.5<br>LAN: 192.168.0.1]
    LAP[Laptop-Wireless<br>DHCP]

    PC --> SW
    PRN --> SW
    WIFI --> SW
    SW --> R1
    R1 --> ISP
    ISP --> DNS
    LAP -. Wi-Fi .-> WIFI