รับทราบ ✨ ผมจะช่วยเขียน **README.md** สำหรับ **Lab Assignment Task 1–5** (Ubuntu + Windows) ให้เป็นเวอร์ชันสมบูรณ์ที่พร้อมส่งได้เลย โดยจะจัดโครงสร้างเป็นระเบียบ มีทั้งรายละเอียดขั้นตอน, คำสั่ง, ไฟล์คอนฟิกที่เกี่ยวข้อง และการแบ่งงานของสมาชิกในกลุ่ม

---

# 📘 README.md

## ENGSE214 – LAB Assignment

**กลุ่มที่:** 8
**สมาชิก:**

* 👤 นาย A – จิรวัฒน์ ขุนเงิน Ubuntu Lab (Day 1)
* 👤 นาย C – ศุภกิตติ์ กุกอง Documentation & Security Checklist

---

## 📑 เนื้อหา

1. Task 1: สร้าง User Accounts สำหรับ Team
2. Task 2: ตั้งค่า Sudo Permissions
3. Task 3: Configure SSH Security
4. Task 4: Set up Firewall Rules
5. Task 5: Enable System Monitoring

---

### 🔹 Task 1 – สร้างบัญชีผู้ใช้ (User Account Management)

```bash
# เพิ่มผู้ใช้ใหม่
sudo adduser alice
sudo adduser bob

# เพิ่มสิทธิ์ sudo ให้ alice
sudo usermod -aG sudo alice

# ตรวจสอบ
id alice
```

📌 **ผลลัพธ์:**

* ผู้ใช้ใหม่สามารถ login ได้
* `alice` สามารถใช้คำสั่ง `sudo`

---

### 🔹 Task 2 – Firewall Rules Setup (UFW)

```bash
# เปิดใช้งาน UFW
sudo ufw enable

# ปรับ default rule
sudo ufw default deny incoming
sudo ufw default allow outgoing

# อนุญาตเฉพาะ SSH (port 2222)
sudo ufw allow 2222/tcp

# ตรวจสอบสถานะ
sudo ufw status verbose
```

📌 **ผลลัพธ์:**

* ปิดกั้นทุก incoming connections ยกเว้น port ที่กำหนด
* ลดความเสี่ยงจากการโจมตี

---

### 🔹 Task 3 – Remote Access (SSH)

```bash
# ติดตั้ง SSH
sudo apt install openssh-server

# แก้ไขพอร์ต SSH
sudo nano /etc/ssh/sshd_config
  Port 2222
  PermitRootLogin no

# restart service
sudo systemctl restart ssh
sudo systemctl status ssh
```

📌 **ผลลัพธ์:**

* เชื่อมต่อได้ผ่าน `ssh -p 2222 alice@<IP>`
* Root ไม่สามารถ login โดยตรง

---

### 🔹 Task 4 – File & Service Configuration

* Apache2 setup

```bash
sudo apt install apache2
sudo systemctl enable apache2
sudo systemctl status apache2
```

* เพิ่มไฟล์คอนฟิก `/etc/apache2/sites-available/lab.conf`

```conf
<VirtualHost *:80>
   ServerName lab.local
   DocumentRoot /var/www/lab
</VirtualHost>
```

📌 **ผลลัพธ์:**

* เข้าถึง web service ได้ที่ `http://<IP>`

---

### 🔹 Task 5 – Enable System Monitoring

ติดตั้งและตั้งค่า **Fail2Ban, Logwatch, Sysstat, Htop**

```bash
sudo apt update
sudo apt install fail2ban logwatch sysstat htop iotop
```

* Fail2Ban Config (`/etc/fail2ban/jail.local`)

```conf
[DEFAULT]
bantime = 3600
findtime = 600
maxretry = 3

[sshd]
enabled = true
port = 2222
logpath = /var/log/auth.log
```

📌 **ผลลัพธ์:**

* Fail2Ban บล็อก IP ที่พยายาม brute-force
* `logwatch --detail High` ใช้สรุป log รายวัน

---

## 🪟 Day 2 – Windows LAB

### 🔹 Task 1 – Configure User Account Control (UAC)

PowerShell:

```powershell
Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" | Select EnableLUA, ConsentPromptBehaviorAdmin
```

* UAC เปิดใช้งาน (ค่า `EnableLUA = 1`)

---

### 🔹 Task 2 – User Accounts

```powershell
$Password = ConvertTo-SecureString "P@ssw0rd123!" -AsPlainText -Force
New-LocalUser -Name "DevUser1" -Password $Password -Description "Developer"
New-LocalUser -Name "TestUser1" -Password $Password -Description "Tester"
New-LocalUser -Name "AdminUser1" -Password $Password -Description "Admin"
```

---

### 🔹 Task 3 – Firewall Setup

```powershell
# ตรวจสอบกฎไฟร์วอลล์
Get-NetFirewallProfile

# อนุญาต RDP
Enable-NetFirewallRule -DisplayGroup "Remote Desktop"
```

---

### 🔹 Task 4 – Remote Desktop Services

* เปิด Remote Desktop
* ตรวจสอบด้วย `mstsc /v:<IP>`

---

### 🔹 Task 5 – Monitoring Tools (Windows)

* Event Viewer
* Task Manager / Performance Monitor
* PowerShell:

```powershell
Get-Process
Get-EventLog -LogName Security -Newest 20
```

---

## 📂 Evidence & Deliverables

* `/Screenshots/Day1/TaskX/` – Ubuntu Proofs
* `/Screenshots/Day2/TaskX/` – Windows Proofs
* `/Configs/` – `.conf`, `.ps1`
* `Security-Checklist.xlsx` – รายการตรวจสอบความปลอดภัย

---

## 📊 Security Checklist (สรุป)

* [x] Disable Root SSH Login
* [x] Change SSH Port
* [x] Enable Firewall Rules
* [x] Fail2Ban Active
* [x] Strong Password Policy
* [x] Regular Log Review

---

## 📝 Analysis & Recommendations

* ปัญหาที่พบ:

  * SSH initial port conflict → แก้ไขโดยเปลี่ยนไปใช้ `2222`
  * Firewall block RDP → ปรับ rule ให้อนุญาตเฉพาะ internal network

* ข้อเสนอแนะ:

  * เพิ่มระบบ Alert อัตโนมัติ (Email/Telegram) จาก Fail2Ban
  * ใช้ ELK stack (Elasticsearch + Kibana) สำหรับ Log visualization
  * สำรอง config เป็น version control (Git)

---