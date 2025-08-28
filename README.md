# 📘 README.md

## ENGSE214 – LAB Assignment

**กลุ่มที่:** 8
**สมาชิก:**

* 👤 นาย A – จิรวัฒน์ ขุนเงิน Ubuntu Lab
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
