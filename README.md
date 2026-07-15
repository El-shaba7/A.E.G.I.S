# A.E.G.I.S 🛡️
**Automated Endpoint Guardian & Intrusion Shield**

An intelligent, active cyber-defense system designed to protect Linux (RHEL) servers from SSH brute-force attacks and lateral movements in real-time. Moving beyond passive logging, A.E.G.I.S detects, blocks, profiles, and alerts at machine speed.

---

## 🚀 Overview

Traditional security tools often rely on passive logging, creating a dwell time that attackers exploit. A.E.G.I.S eliminates this gap. Operating primarily on Red Hat Enterprise Linux (RHEL), it monitors authentication logs (`/var/log/secure`), automatically blocks malicious IPs using Firewalld, gathers threat intelligence via Nmap, and triggers a multi-channel alert system (Telegram & physical Arduino alarm) — all within 500 milliseconds.

## ✨ Core Features (The 6-Engine Architecture)

1. **Real-Time Log Monitoring:** Tail-follows `/var/log/secure` to detect failed SSH login attempts instantly with near-zero CPU overhead.
2. **Autonomous Blocking Shield:** Automatically implements permanent `Firewalld` rich rules to drop an attacker's IP after exactly 5 failed attempts.
3. **Forensic Profiling:** Triggers a background `Nmap` OS fingerprinting and ARP-based MAC/OUI lookup to identify the attacker's hardware and operating system.
4. **Honeypot Deception Layer:** Deploys a decoy file (`server_passwrds.txt`) monitored by the Linux `auditd` framework to trap intruders attempting lateral movement.
5. **Dual-Channel Alert System:** * **Digital:** Instant Telegram Bot notifications with attack details and intelligence reports.
   * **Physical:** Serial communication to an Arduino Uno, triggering a customized Red LED & Buzzer alarm pattern.
6. **Live SOC Dashboard:** A Flask-based web interface providing real-time system metrics (CPU/RAM), live traffic logs, threat intelligence, and a manual hardware "Disarm" button.

---

## 🛠️ Tech Stack & Requirements

* **Operating System:** Red Hat Enterprise Linux (RHEL) or compatible Linux distribution.
* **Programming Languages:** Python 3, C++ (Arduino), HTML/CSS/JavaScript.
* **System Packages:** `firewalld`, `nmap`, `auditd`
* **Python Libraries:** `flask`, `pyserial`, `psutil`, `requests`
* **Hardware:** Arduino Uno, 3x Red LEDs, 3x Green LEDs, 1x Active Buzzer, jumper wires.

---

## 🔌 Hardware Setup (Arduino)

Connect your Arduino Uno to the server via USB (defaults to `/dev/ttyACM0`). Wire the components to the following pins:
* **Green LEDs (Safe Mode):** Pins `12`, `9`, `7`
* **Red LEDs (Alert Mode):** Pins `13`, `11`, `8`
* **Buzzer:** Pin `10`

*Flash the `arduino.cpp` code to your Arduino board before starting the main Python script.*

---

## ⚙️ Installation & Configuration

**1. System Preparation & Dependencies**
```bash
sudo yum update -y
sudo yum install firewalld nmap audit python3-pip -y
pip3 install flask pyserial psutil requests

2. SSH Hardening (Recommended)
Edit /etc/ssh/sshd_config to reduce attack surface:
PermitRootLogin no
MaxAuthTries 6
Restart SSH: sudo systemctl restart sshd

3. Honeypot Setup
Create the bait directory and file, then set up the auditd watch rule:
sudo mkdir -p /home/sysadmin/.ssh_keys_backup
echo 'db_user: admin' | sudo tee /home/sysadmin/.ssh_keys_backup/server_passwrds.txt > /dev/null
sudo auditctl -w /home/sysadmin/.ssh_keys_backup/server_passwrds.txt -p rwa -k HONEYPOT_TRIPPED

4. Configuration
In main.py, ensure you update your Telegram API credentials:
TELEGRAM_TOKEN = "YOUR_BOT_TOKEN_HERE"
TELEGRAM_CHAT_ID = "YOUR_CHAT_ID_HERE"

🚀 Running the System
Start the A.E.G.I.S SOC server with root privileges (required for Firewalld and Nmap execution):
sudo python3 main.py

Access the dashboard by navigating to http://<SERVER_IP>:5000 in your web browser.
Default Admin ID: aegis_soc
Default Passcode: Aeg!s@Soc#

👥 Team & Acknowledgments
This project was developed as a Work-Based Professional Project at Sphinx University - Faculty of Computers & Artificial Intelligence, in collaboration with the Information Technology Institute (ITI).

The Team:
Mohamed Mahmoud Saleh
Mohamed Hazem Bakr
Heba Nejm Mahani
Noorhan Yasser Reffat
Ahmed Al Shazli

Supervised by:
Eng. Ahmed Hussein Saleh - Eng. Ekram Abd Elwahab

Disclaimer: This system was built for educational and defensive purposes. Always ensure you have explicit permission before running scanning tools like Nmap on any network.
