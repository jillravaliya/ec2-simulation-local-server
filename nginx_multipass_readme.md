# Linux Server Fundamentals — Local Cloud Infrastructure Setup

[![Linux](https://img.shields.io/badge/Linux-Ubuntu_22.04-FCC624?logo=ubuntu&logoColor=black)](https://ubuntu.com/)
[![NGINX](https://img.shields.io/badge/NGINX-Web_Server-009639?logo=nginx&logoColor=white)](https://nginx.org/)
[![Multipass](https://img.shields.io/badge/Multipass-VM_Management-E95420?logo=canonical&logoColor=white)](https://multipass.run/)
[![SSH](https://img.shields.io/badge/SSH-Secure_Shell-4D4D4D?logo=gnubash&logoColor=white)](https://www.openssh.com/)

---

## 📖 What This Is

This project is the **entry point into real cloud and infrastructure engineering.**

Instead of running code on your laptop, you run it on a **remote Linux server** — a real machine simulating AWS EC2 — and understand how servers, networks, and web delivery actually work.

Modern **DevOps, Cloud, and SRE roles** all revolve around one core capability:

> **Taking an application and making it run reliably on a real machine accessible to the public.**

This project forces you to learn the fundamentals that every advanced tool (Docker, Kubernetes, Terraform, CI/CD) depends on.

---

## 🎯 What You'll Learn

By completing this project, you will understand:

| **What You Did** | **What You Learned** |
|------------------|----------------------|
| Created a cloud VM | EC2 fundamentals, VM provisioning |
| Connected via SSH | Linux navigation, permissions, authentication |
| Installed a web server | Web server basics (NGINX/Apache) |
| Deployed a simple HTML page | How a server delivers content |
| Made it publicly accessible | Ports, IP addressing, firewall rules |
| Broke things and fixed them | Logs, debugging, troubleshooting |

**This is the foundation for DevOps + Cloud.**

---

## 🏆 Outcome

A simple website running publicly on a local Linux server, managed entirely by you through the terminal.

**Skills demonstrated:**
- Linux server administration
- NGINX web server configuration
- Network troubleshooting
- Log analysis and debugging
- Virtual host configuration
- Reverse proxy setup

---

## 🚀 Project Setup

### **Goal**

Create a Linux server on your Mac that behaves exactly like an AWS EC2 instance, so you can learn Linux, networking, NGINX, SSH, ports, and server debugging **without needing a cloud account**.

### **Why Multipass?**

Multipass is created by **Canonical** (Ubuntu developer) and provides instant, terminal-only Ubuntu servers using cloud-init. It behaves like a real cloud VM (EC2) and avoids GUI setup, ISO installation, and heavy virtualization.

**Perfect for DevOps learning.**

---

## 📋 Prerequisites

- macOS with Homebrew installed
- Terminal access
- Basic command-line knowledge

---

## 🛠️ Step-by-Step Guide

### **Step 1: Install Multipass**

Install Multipass (our "local EC2 system"):

```bash
brew install --cask multipass
multipass version
```

**What this does:**
- `brew` = Homebrew package manager
- `install` = download + install
- `--cask` = install a macOS application
- `multipass` = the app we are installing
- `multipass version` = verify installation

This installs the **Multipass daemon** that manages local Linux VMs like **mini cloud servers**.

![Screenshot 2025-11-22 at 1.07.47 AM.png](screenshots/Screenshot%202025-11-22%20at%201.07.47%20AM.png)

---

### **Step 2: Launch Your Ubuntu Server**

Create a VM exactly like AWS EC2, fully through terminal:

```bash
multipass launch --name devops-server --mem 2G --disk 20G --cpus 2
multipass list
```

**Command breakdown:**

- `multipass launch` → Like running `aws ec2 run-instances`
- `--name devops-server` → Instance name (like EC2 Name Tag)
- `--mem 2G` → 2 GB memory allocation
- `--disk 20G` → 20 GB virtual disk (like EBS volume)
- `--cpus 2` → 2 CPU cores assigned

**What happens after you run it:**

Multipass automatically:
- Downloads official Ubuntu Server 22.04 cloud image
- Auto-configures networking, hostname, cloud-init
- Creates login user ("ubuntu")
- Boots the VM automatically
- Prepares SSH access internally
- **Within ~10 seconds you have a running Linux server**

---

### **Step 3: SSH Into Your Server**

This is the moment you "enter" the Linux machine:

```bash
multipass shell devops-server
```

**What this does:**
- Opens an interactive shell **inside the VM**
- You become the "ubuntu" user (same as EC2)
- Identical to: `ssh ubuntu@<your-ec2-ip>`

**Expected prompt change:**

Your terminal switches from:
```
jillravaliya@Jills-MacBook-Air ~ %
```

To:
```
ubuntu@devops-server:~$
```

![Screenshot 2025-11-22 at 1.30.02 AM.png](screenshots/Screenshot%202025-11-22%20at%201.30.02%20AM.png)

**YOU ARE NOW INSIDE THE SERVER.**

From here onward, every command affects your Linux machine. This is where real DevOps learning begins.

---

### **Problem Faced: SSH Permission Denied**

When you run `multipass shell devops-server`, internally Multipass:
- Uses its own private key for that VM
- Opens an SSH connection: `ssh -i /path/to/multipass/private_key ubuntu@192.168.2.2`
- Gives you a shell automatically

**YOU don't need the key files — Multipass handles everything.**

![Screenshot 2025-11-22 at 1.36.49 AM.png](screenshots/Screenshot%202025-11-22%20at%201.36.49%20AM.png)

**What I Learned:**
- Multipass manages SSH automatically
- `multipass shell <vm-name>` is the correct access method
- Direct SSH to VM IP fails without exporting the VM's private key
- This is exactly how EC2 works: wrong key → `Permission denied (publickey)`

---

### **Step 4: Update Your Server**

Run inside your VM:

```bash
sudo apt update && sudo apt upgrade -y
```

**Command breakdown:**
- `apt update` → Refreshes package index
- `apt upgrade` → Installs latest versions
- `sudo` → Required for system changes

This is **EXACTLY** what you do on EC2 right after launching.

![Screenshot 2025-11-22 at 1.54.59 AM.png](screenshots/Screenshot%202025-11-22%20at%201.54.59%20AM.png)

**What that message means:**

During the update, Ubuntu installed a **new kernel version**. When a new kernel installs, the system needs a **reboot** to start using it.

Message:
```
Restarting the system to load the new kernel will not be handled automatically
```

Translation:
> "Bro, I installed a new kernel. I won't reboot myself. You need to reboot me."

**This is NORMAL.** Every real server does this.

---

### **Step 5: Reboot Your Server**

Inside the VM, run:

```bash
sudo reboot
```

This will:
- Restart the VM
- Load the new kernel
- Apply system-level updates

![Screenshot 2025-11-22 at 2.03.29 AM.png](screenshots/Screenshot%202025-11-22%20at%202.03.29%20AM.png)

Your shell will close automatically.

---

### **Step 6: Reconnect to the Server**

After 5–10 seconds, reconnect:

```bash
multipass shell devops-server
```

Expected prompt:
```
ubuntu@devops-server:~$
```

![Screenshot 2025-11-22 at 2.05.56 AM.png](screenshots/Screenshot%202025-11-22%20at%202.05.56%20AM.png)

---

### **Step 7: Install NGINX**

This is your first real "production-grade" service.

**NGINX runs 40% of the world's websites — this is NOT a toy.**

```bash
sudo apt install nginx -y
```

**Command breakdown:**
- `apt` = Ubuntu package manager
- `install nginx` = Download the NGINX web server
- `-y` = Auto-confirm installation
- `sudo` = Required because this modifies system files

![Screenshot 2025-11-22 at 2.16.54 AM.png](screenshots/Screenshot%202025-11-22%20at%202.16.54%20AM.png)

**Ubuntu will:**
- Download NGINX
- Install config files
- Create systemd service
- Open port 80 internally
- Start it automatically

This is **EXACTLY** what happens on EC2.

---

### **Step 8: Check if NGINX is Running**

```bash
systemctl status nginx
```

You should see:
```
active (running)
```

![Screenshot 2025-11-22 at 2.20.24 AM.png](screenshots/Screenshot%202025-11-22%20at%202.20.24%20AM.png)

**What's happening:**

`systemctl` = Linux "service controller"

It tells you if a service is:
- **running** ✅
- **stopped** ⏸️
- **failed** ❌
- **restarting** 🔄

**This is CRITICAL DevOps knowledge.**

---

### **Step 9: View NGINX in Browser**

From inside the VM:
```bash
exit
```

On your Mac:
```bash
multipass list
```

Copy the **IPv4 address**, then open in your browser:
```
http://<your-ip>
```

You should see the **NGINX Welcome Page**.

![Screenshot 2025-11-22 at 2.32.56 AM.png](screenshots/Screenshot%202025-11-22%20at%202.32.56%20AM.png)

---

### **Step 10: Deploy Your Own Website**

Right now, NGINX is serving its **default page** from:
```
/var/www/html/index.nginx-debian.html
```

We will replace that with **your own** website.

---

#### **Step 10.1: Replace the Default Page**

On your Mac terminal:
```bash
multipass shell devops-server
```

Create your own webpage:
```bash
echo "<h1>Hello Jill — Your first deployed page!</h1>" | sudo tee /var/www/html/index.html
```

**Command breakdown:**
- `echo "..."` → Creates text
- `|` → Sends that text to next command
- `sudo tee file` → Writes to a file requiring root permissions
- `/var/www/html/` → Default NGINX website directory
- `index.html` → Main page served on `/`

This will **immediately** overwrite the default NGINX page.

---

#### **Step 10.2: Refresh Your Browser**

Verify the file exists:
```bash
ls -l /var/www/html/
```

You should see:
```
index.html
```

**Open again:**
```
http://<your-ip>
```

![Screenshot 2025-11-22 at 2.55.58 AM.png](screenshots/Screenshot%202025-11-22%20at%202.55.58%20AM.png)

You should now see:

**"Hello I am Jill Ravaliya - This is my First Deployed Page!"**

🎉 **Congratulations** — you just deployed your first website on a real server (not localhost).

This is **EXACTLY** how hosting works.

---

## 🔧 Breaking and Fixing NGINX

You don't become DevOps by "installing stuff."

You become DevOps by **breaking systems and recovering them**.

---

### **Exercise 1: Break NGINX (Stop the Service)**

Run inside the VM:
```bash
sudo systemctl stop nginx
```

**What happens:**
- NGINX stops
- Port 80 becomes unused
- Browser shows **"site cannot be reached"**

This is **EXACTLY** what happens in production when:
- Service crashes
- Deployment fails
- Config error happens
- Systemd doesn't restart

Confirm:
```bash
systemctl status nginx
```

![Screenshot 2025-11-22 at 3.06.56 AM.png](screenshots/Screenshot%202025-11-22%20at%203.06.56%20AM.png)

You should see:
```
inactive (dead) or stopped
```

**Check browser:**

![Screenshot 2025-11-22 at 3.08.33 AM.png](screenshots/Screenshot%202025-11-22%20at%203.08.33%20AM.png)

Website should NOT work.

---

### **Exercise 1 Fix: Start NGINX Again**

Run:
```bash
sudo systemctl start nginx
systemctl status nginx
```

![Screenshot 2025-11-22 at 3.10.30 AM.png](screenshots/Screenshot%202025-11-22%20at%203.10.30%20AM.png)

You should see:
```
active (running)
```

**Check browser again:**

![Screenshot 2025-11-22 at 3.13.16 AM.png](screenshots/Screenshot%202025-11-22%20at%203.13.16%20AM.png)

Browser should work again. ✅

---

### **Exercise 2: Break NGINX Config**

This simulates a classic real-world error.

Run:
```bash
sudo nano /etc/nginx/nginx.conf
```

Inside nano:
- Go to the **FIRST LINE**
- Add random characters like `hello world garbage`

![Screenshot 2025-11-22 at 3.28.04 AM.png](screenshots/Screenshot%202025-11-22%20at%203.28.04%20AM.png)

Save and exit:
- `Ctrl + O`
- `Enter`
- `Ctrl + X`

Now restart NGINX:
```bash
sudo systemctl restart nginx
```

**Expected result:**

This will **FAIL**. NGINX will NOT restart.

Error message:
```
Job for nginx.service failed because the control process exited with error code
```

![Screenshot 2025-11-22 at 3.29.18 AM.png](screenshots/Screenshot%202025-11-22%20at%203.29.18%20AM.png)

---

### **Debugging NGINX Config Errors**

When NGINX fails due to config syntax error, there are **three ways** to check:

#### **1. NGINX Syntax Test (MOST IMPORTANT)**

```bash
sudo nginx -t
```

**Use this FIRST.** This directly shows config syntax errors.

Example output:
```
[emerg] unknown directive "hello" in /etc/nginx/nginx.conf:2
nginx: configuration file /etc/nginx/nginx.conf test failed
```

#### **2. Systemctl Status**

```bash
systemctl status nginx
```

Shows:
```
Active: failed (Result: exit-code)
```

#### **3. Journalctl Logs**

```bash
sudo journalctl -u nginx --no-pager --since "2 min ago"
```

If NGINX **never started** (syntax test failed before startup):
```
-- No entries --
```

![Screenshot 2025-11-22 at 3.35.16 AM.png](screenshots/Screenshot%202025-11-22%20at%203.35.16%20AM.png)

**This teaches you:**
- How to debug broken configs
- How Linux logs work
- How systemd logs errors
- How real servers fail

---

### **Exercise 2 Fix: Repair the Config**

Open the config again:
```bash
sudo nano /etc/nginx/nginx.conf
```

![Screenshot 2025-11-22 at 3.50.11 AM.png](screenshots/Screenshot%202025-11-22%20at%203.50.11%20AM.png)

Remove the garbage text you added.

Save → Exit

Reload NGINX:
```bash
sudo systemctl restart nginx
```

![Screenshot 2025-11-22 at 3.51.18 AM.png](screenshots/Screenshot%202025-11-22%20at%203.51.18%20AM.png)

Browser → Refresh → Back online. ✅

---

### **Exercise 3: Break File Permissions**

This simulates a silent error where the file exists but the server can't read it.

```bash
sudo chmod 000 /var/www/html/index.html
```

**Command breakdown:**
- `chmod` = "change mode" → modifies file permissions
- `000` = Remove **all** permissions for **everyone**

Check permissions:
```bash
ls -l /var/www/html/index.html
```

Expected output:
```
---------- 1 root root 68 Nov 22 02:52 /var/www/html/index.html
```

![Screenshot 2025-11-22 at 11.33.07 AM.png](screenshots/Screenshot%202025-11-22%20at%2011.33.07%20AM.png)

**Permissions in Linux have 3 groups:**
- **owner**
- **group**
- **others**

Each group has:
- **read (r)**
- **write (w)**
- **execute (x)**

When you set `000`, you're telling Linux:
> "Nobody — not even the file owner — can read, write, or execute this file."

**WHY THIS BREAKS THE WEBSITE:**

NGINX needs to **read** the HTML file to serve it.

You just removed:
- Owner read
- Group read
- Others read

So NGINX tries to open the file → Linux denies.

![Screenshot 2025-11-22 at 11.35.07 AM.png](screenshots/Screenshot%202025-11-22%20at%2011.35.07%20AM.png)

Browser gets:
- **403 Forbidden**
- Or NGINX error page

This is **EXACTLY** how permission bugs happen in real systems.

---

### **Exercise 3 Fix: Restore Permissions**

```bash
sudo chmod 644 /var/www/html/index.html
```

**WHY 644?**

Break it down:
- **6** (owner) = read + write
- **4** (group) = read
- **4** (others) = read

![Screenshot 2025-11-22 at 11.34.19 AM.png](screenshots/Screenshot%202025-11-22%20at%2011.34.19%20AM.png)

| User | Permission | Meaning |
|------|------------|---------|
| Owner | rw- (6) | Can read + write |
| Group | r-- (4) | Can read |
| Others | r-- (4) | Can read |

NGINX runs as user `www-data` → part of "others" or "group" → needs **read**.

**644 is the standard permission for web files.**

---

## 📊 Understanding Logs

Logs are your eyes. They tell you **EXACTLY** what the server is doing.

---

### **Access Log**

```bash
sudo tail -f /var/log/nginx/access.log
```

**Command breakdown:**
- `tail` = Show last few lines of file
- `-f` = Follow (live stream updates)

This file shows **every request** your server receives.

Example line:
```
192.168.2.1 - - [22/Nov/2025:02:40:22 +0530] "GET / HTTP/1.1" 200 150 "-" "Mozilla/5.0"
```

![Screenshot 2025-11-22 at 11.45.45 AM.png](screenshots/Screenshot%202025-11-22%20at%2011.45.45%20AM.png)

**Meaning:**
- `192.168.2.1` → Your Mac accessing the server
- `GET /` → Request for homepage
- `200` → Success → NGINX returned your custom HTML
- `150 bytes` → Size of response
- `User Agent` → Browser information

**This proves:**
- Server is reachable
- NGINX is responding normally
- Network is working
- Custom HTML is being delivered

**Common status codes:**
- `404` = File missing
- `403` = Permission denied
- `500` = Server error

This is how you **debug users hitting your site**.

---

### **Error Log**

```bash
sudo tail -f /var/log/nginx/error.log
```

Shows server-side errors.

![Screenshot 2025-11-22 at 12.01.50 PM.png](screenshots/Screenshot%202025-11-22%20at%2012.01.50%20PM.png)

Example message:
```
[notice] 1251#1251: using inherited sockets from "5;6;"
```

**Breakdown:**
- `notice` → NOT an error, just informational
- `using inherited sockets` → NGINX reused open socket (port 80)
- Happens when NGINX restarts smoothly
- Systemd restarted NGINX and re-used existing open port

**This is a NORMAL message during restart.**

**Other examples:**

**Missing file:**
```
open() "/var/www/html/index.html" failed (2: No such file or directory)
```

**Permission fail:**
```
open() "/var/www/html/index.html" failed (13: Permission denied)
```

**Config error:**
```
unknown directive "xyz"
```

**Port conflict:**
```
bind() to 0.0.0.0:80 failed (98: Address already in use)
```

If NGINX fails to start, error log tells you **exact reason**.

Reading logs = understanding the system talking to you.

**This is how real DevOps engineers fix outages.**

---

## 🌐 Understanding Ports, Sockets, and Binding

---

### **What is a PORT?**

A **port** is just a "door" on your server that programs use to communicate.

Examples:
- NGINX uses **port 80**
- HTTPS uses **443**
- SSH uses **22**
- Backend APIs often use **3000 / 5000**
- Databases use **3306 (MySQL)**, **5432 (PostgreSQL)**

**Think of it this way:**

Your server has:
- **1 IP address**
- **65,535 ports**

Each port can run a different service.

---

### **What is BINDING?**

Binding means:
> A process attaches itself to a port so it can listen for traffic.

Examples:
- NGINX **binds** to port 80
- Your backend app **binds** to port 5000

**Only ONE process can bind to a port at a time.**

---

### **What is a SOCKET?**

A **socket** = (IP address + port number)

Example: `192.168.2.2:80`

This is how Linux identifies an open network connection.

---

### **Why This Matters in DevOps**

When two services try to use the same port, you get the classic error:

```
bind() to 0.0.0.0:80 failed (98: Address already in use)
```

Translation:
> "Port 80 is already taken — I cannot start."

NGINX, Docker, Kubernetes, Node apps… **EVERYTHING fails** with this if you don't understand binding.

---

### **Check What Ports Are Open**

Run inside your VM:
```bash
sudo ss -tulnp
```

**Command breakdown:**
- `ss` = Socket statistics (modern version of netstat)
- `-t` = TCP
- `-u` = UDP
- `-l` = Listening ports
- `-n` = Show raw numbers (not names)
- `-p` = Show which program is using each port

![Screenshot 2025-11-22 at 12.30.54 PM.png](screenshots/Screenshot%202025-11-22%20at%2012.30.54%20PM.png)

Example output:
```
LISTEN 0 511 0.0.0.0:80   *:*   users:(("nginx",pid=1234,fd=6))
LISTEN 0 128 0.0.0.0:22   *:*   users:(("sshd",pid=875,fd=3))
```

This tells you:
- Port **80** → NGINX
- Port **22** → SSH daemon
- Process name + PID included

---

### **Simulate a Port Conflict**

Run:
```bash
sudo python3 -m http.server 80
```

![Screenshot 2025-11-22 at 12.33.05 PM.png](screenshots/Screenshot%202025-11-22%20at%2012.33.05%20PM.png)

This will **FAIL** with:
```
OSError: [Errno 98] Address already in use
```

Because NGINX is already using port 80.

**THIS is exactly what "port binding conflict" looks like in the real world.**

---

## ⚙️ Custom NGINX Virtual Host

Right now your server uses the **DEFAULT** config:
```
/etc/nginx/sites-available/default
```

**Real production servers NEVER use the default file.**

They create **their own site configs**.

We will do **EXACTLY** that — like a real DevOps engineer.

---

### **Step 14.1: Create Production Folder Structure**

Inside the VM:
```bash
sudo mkdir -p /var/www/jillapp/public
```

This creates:
```
/var/www/jillapp/public/
```

This is now **YOUR** web root.

Add a test page:
```bash
echo "<h1>Jill App Served From Custom NGINX Config</h1>" | sudo tee /var/www/jillapp/public/index.html
```

---

### **Step 14.2: Create Custom NGINX Config**

Run:
```bash
sudo nano /etc/nginx/sites-available/jillapp
```

Put this content:

```nginx
server {
    listen 80;
    server_name jillapp.local;

    root /var/www/jillapp/public;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

![Screenshot 2025-11-22 at 1.06.28 PM 1.png](screenshots/Screenshot%202025-11-22%20at%201.06.28%20PM%201.png)

**Explanation:**
- `listen 80` → Accept HTTP traffic
- `server_name jillapp.local` → Identifier (local domain)
- `root` → Your app's folder
- `try_files` → Safe static file serving

This is a **real production virtual host**, minimal but correct.

Save: `Ctrl + O` → `Enter` → `Ctrl + X`

---

### **Step 14.3: Enable Your New Site**

Run:
```bash
sudo ln -s /etc/nginx/sites-available/jillapp /etc/nginx/sites-enabled/
```

This activates the config.

Remove the default site:
```bash
sudo rm /etc/nginx/sites-enabled/default
```

Test your config:
```bash
sudo nginx -t
```

![Screenshot 2025-11-22 at 1.06.28 PM.png](screenshots/Screenshot%202025-11-22%20at%201.06.28%20PM.png)

Expected:
```
syntax is ok
test is successful
```

Restart NGINX:
```bash
sudo systemctl restart nginx
```

---

### **Step 14.4: Visit Your Custom Config**

Open browser:
```
http://192.168.2.2
```

You should now see:
```
Jill App Served From Custom NGINX Config
```

![Screenshot 2025-11-22 at 1.10.17 PM.png](screenshots/Screenshot%202025-11-22%20at%201.10.17%20PM.png)

🎉 **You just created your first real NGINX virtual host**, same as a DevOps engineer would on production servers.

---

## 🔄 Reverse Proxy (API Routing)

This is **REAL DevOps work:**

- Frontend on port **80**
- Backend on port **5000**
- NGINX forwards `/api` → backend
- Browser still hits port 80
- Server handles routing internally

This is **EXACT** behavior you use in:
- Docker
- Kubernetes
- Load balancers
- CI/CD deployments
- Multi-service apps

---

### **Step 15.1: Create a Fake Backend**

Inside your VM:
```bash
sudo apt install -y python3
python3 -m http.server 5000
```

![Screenshot 2025-11-22 at 1.20.59 PM.png](screenshots/Screenshot%202025-11-22%20at%201.20.59%20PM.png)

This:
- Runs a backend HTTP server
- Listens on port 5000
- Responds with directory listing
- Used ONLY for testing reverse proxy

Open a **second terminal tab** and run:
```bash
multipass shell devops-server
```

We'll edit NGINX in the second tab while the backend server runs in the first.

---

### **Step 15.2: Update NGINX Config for Reverse Proxy**

Open your custom config:
```bash
sudo nano /etc/nginx/sites-available/jillapp
```

Update to:

```nginx
server {
    listen 80;
    server_name jillapp.local;

    root /var/www/jillapp/public;
    index index.html;

    # Serve frontend
    location / {
        try_files $uri $uri/ =404;
    }

    # Reverse proxy for backend API
    location /api/ {
        proxy_pass http://127.0.0.1:5000/;
    }
}
```

![Screenshot 2025-11-22 at 1.26.28 PM.png](screenshots/Screenshot%202025-11-22%20at%201.26.28%20PM.png)

**What this does:**
- `/` → Serve frontend
- `/api/` → Forward to backend server on port 5000

This is **EXACTLY** how microservices communicate.

Save + Exit

---

### **Step 15.3: Test NGINX Config**

Run:
```bash
sudo nginx -t
```

Expected:
```
syntax is ok
test is successful
```

![Screenshot 2025-11-22 at 1.27.13 PM.png](screenshots/Screenshot%202025-11-22%20at%201.27.13%20PM.png)

Restart NGINX:
```bash
sudo systemctl restart nginx
```

---

### **Step 15.4: Test Reverse Proxy**

**Test frontend:**

Open:
```
http://192.168.2.2
```

![Screenshot 2025-11-22 at 1.28.37 PM.png](screenshots/Screenshot%202025-11-22%20at%201.28.37%20PM.png)

You should see your HTML.

**Test backend through reverse proxy:**

Open:
```
http://192.168.2.2/api/
```

You should see Python server output (directory listing).

---

### **Common Issue: Port Already in Use**

If you see:
```
OSError: [Errno 98] Address already in use
```

![Screenshot 2025-11-22 at 1.42.14 PM.png](screenshots/Screenshot%202025-11-22%20at%201.42.14%20PM.png)

**What happened:**
- Port 5000 is already in use
- Python backend cannot start
- Reverse proxy fails

**Fix:**

Check what's using port 5000:
```bash
sudo ss -tulnp | grep 5000
```

![Screenshot 2025-11-22 at 