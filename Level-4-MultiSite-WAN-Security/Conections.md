| ชื่ออุปกรณ์       | พอร์ต / อินเทอร์เฟซ | IP Address   | Subnet Mask     | Default Gateway | หมายเหตุ / หน้าที่                                   |
| ----------------- | ------------------- | ------------ | --------------- | --------------- | ---------------------------------------------------- |
| **ISP_Router**    | GigabitEthernet0/0  | 200.0.0.1    | 255.255.255.252 | N/A             | ลิงก์หลักเชื่อมต่อไปยัง HQ_Router                    |
| **ISP_Router**    | GigabitEthernet0/1  | 200.0.0.5    | 255.255.255.252 | N/A             | ลิงก์หลักเชื่อมต่อไปยัง Branch_Router                |
| **HQ_Router**     | GigabitEthernet0/0  | 200.0.0.2    | 255.255.255.252 | N/A             | ขา WAN (Public IP) ของสำนักงานใหญ่                   |
| **HQ_Router**     | GigabitEthernet0/1  | 10.0.10.1    | 255.255.255.0   | N/A             | ขา LAN และ Gateway ของสำนักงานใหญ่                   |
| **HQ_Router**     | Tunnel0             | 172.16.0.1   | 255.255.255.252 | N/A             | ต้นทาง GRE over IPsec VPN Tunnel                     |
| **Branch_Router** | GigabitEthernet0/0  | 200.0.0.6    | 255.255.255.252 | N/A             | ขา WAN (Public IP) ของสาขาย่อย                       |
| **Branch_Router** | GigabitEthernet0/1  | 192.168.30.1 | 255.255.255.0   | N/A             | ขา LAN และ Gateway ของสาขาย่อย                       |
| **Branch_Router** | Tunnel0             | 172.16.0.2   | 255.255.255.252 | N/A             | ปลายทาง GRE over IPsec VPN Tunnel                    |
| **AAA_Server**    | FastEthernet0       | 10.0.10.10   | 255.255.255.0   | 10.0.10.1       | ฐานข้อมูลผู้ใช้งานส่วนกลาง (RADIUS)                  |
| **WLC_HQ**        | Management          | 10.0.10.20   | 255.255.255.0   | 10.0.10.1       | ระบบควบคุม Wi-Fi รวมศูนย์ (Controller)               |
| **LAP_HQ**        | FastEthernet0       | DHCP         | DHCP            | DHCP            | Lightweight Access Point กระจายสัญญาณที่สำนักงานใหญ่ |
| **PC_HQ**         | FastEthernet0       | DHCP         | DHCP            | DHCP            | เครื่องแอดมินสำหรับตั้งค่า WLC ผ่าน Web GUI          |
| **Laptop0**       | Wireless0           | DHCP         | DHCP            | DHCP            | เครื่องทดสอบระบบ 802.1X (Corporate_WiFi)             |
| **PC_Branch**     | FastEthernet0       | DHCP         | DHCP            | DHCP            | เครื่องผู้ใช้งานสาขาสำหรับทดสอบ Ping ข้าม VPN        |
