# DNS-Attack-Lab

# 🔐 DNS Reconnaissance & Zone Transfer Attack Lab (AXFR)

## 📌 Overview

This project demonstrates how attackers perform DNS reconnaissance and exploit a misconfigured DNS server using a **Zone Transfer (AXFR) attack**. It also shows how to detect such attacks using **manual log analysis**, without using SIEM tools.

---

## 🖧 Architecture Diagram
<img width="1024" height="1536" alt="architecture diagram" src="https://github.com/user-attachments/assets/f3efe386-eb54-4032-997e-0b1547d088ab" />


## 🎯 Objectives

* Understand DNS fundamentals (A, NS, SOA records)
* Perform DNS enumeration using `dig`
* Exploit DNS misconfiguration (Zone Transfer)
* Detect attacks using system logs
* Apply mitigation techniques

---

## 🛠️ Tools & Technologies

* Kali Linux (Attacker)
* Ubuntu Server (BIND DNS Server)
* BIND9 (DNS service)
* dig, dnsenum, nmap

---

## 🖥️ Lab Architecture

```
Kali Linux (Attacker)
        ↓
Ubuntu DNS Server (BIND - Vulnerable)
        ↓
Internal Hosts (web, dev, vpn)
```

---

## ⚙️ DNS Configuration (Vulnerable)

```
zone "lab.local" {
    type master;
    file "/etc/bind/db.lab.local";
    allow-transfer { any; };   // Vulnerable configuration
};
```

---

## 📄 Zone File Example

```
$TTL 604800
@   IN  SOA ns1.lab.local. admin.lab.local. (
        2026033001
        604800
        86400
        2419200
        604800 )

@       IN  NS      ns1.lab.local.
ns1     IN  A       192.168.1.77
web     IN  A       192.168.1.20
dev     IN  A       192.168.1.60
vpn     IN  A       192.168.1.70
```

---

## 🔍 Reconnaissance Phase

```
dig lab.local
dig NS lab.local
dig ANY lab.local
```

---

## 🚨 Zone Transfer Attack (AXFR)

```
dig axfr @192.168.1.77 lab.local
```

### 📌 Result:

* Extracted internal records:

  * web.lab.local
  * dev.lab.local
  * vpn.lab.local

---

## 🧠 Impact

* Exposure of internal infrastructure
* Increased attack surface
* Enables further exploitation

---

## 🔎 Detection (Manual Log Analysis)

### Monitor logs:

```
tail -f /var/log/syslog
```

### Identify attack:

```
grep AXFR /var/log/syslog
```

### Example log:

```
client 192.168.1.X: query: lab.local IN AXFR
```

---

## 🛡️ Mitigation

Update configuration:

```
allow-transfer { none; };
```

Restart DNS:

```
sudo systemctl restart bind9
```

---

## ✅ Verification

```
dig axfr @192.168.1.77 lab.local
```

✔ Expected Result: Zone transfer should fail

---

## 📊 Key Learnings

* DNS plays a critical role in the reconnaissance phase
* Misconfigured DNS can expose sensitive internal data
* AXFR is a high-risk vulnerability
* Detection is possible using basic system logs
* Proper configuration prevents exploitation

---

## 📸 Screenshots


* AXFR Attack Output
 <img width="989" height="476" alt="Screenshot 2026-03-31 131708" src="https://github.com/user-attachments/assets/0c4ccae2-817c-444c-ba66-973e68ee2977" />


* DNS Configuration
 <img width="1018" height="294" alt="Screenshot 2026-03-31 131556" src="https://github.com/user-attachments/assets/0f8cf387-199a-41b2-930e-934bee6b7b86" />


* Attack Log Detection
 <img width="1284" height="258" alt="Screenshot 2026-03-31 131809" src="https://github.com/user-attachments/assets/0df600a1-3951-4de3-9989-0f71dc38b66b" />


* Mitigation Configuration
 <img width="375" height="19" alt="image" src="https://github.com/user-attachments/assets/97e4b6c8-ef85-4346-97fb-81a8d1251885" />


* Mitigation Result
 <img width="1069" height="206" alt="image" src="https://github.com/user-attachments/assets/4e05cf2b-a3fe-469e-8005-094f42c41602" />


* Final Log Detection
 <img width="1280" height="247" alt="image" src="https://github.com/user-attachments/assets/deb5ef1f-a8ec-40b6-afc2-0c231a6d47c3" />

---

## 🚀 Future Improvements

* Integrate SIEM tools 
* Detect DNS tunneling attacks
* Automate log monitoring
* Add alerting mechanisms

---

## 👨‍💻 Author

**Prince Baruwala**

---

## ⭐ Contribution

Feel free to fork this repository and enhance the project!
