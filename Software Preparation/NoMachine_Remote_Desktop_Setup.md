# NoMachine Setup Guide (Onboard Computer Remote Access)

This guide shows how to install **NoMachine** and remotely connect to the another computer (e.g., Raspberry Pi, Jetson, Linux PC) from your **MacBook, Windows, or Linux computer**.

Note: It is best to use NoMachine with an HDMI dummy on the onboard computer. A lot of single board computers will not run display related tasks if no HDMI cable is connected. Therefore you will most likely not be able to connect to the onboard computer if nothing is in the HDMI port.

---

## What is NoMachine?

**NoMachine** is a remote desktop tool that lets you access another computer’s screen and control it from another computer.

It has similar features to services like:

* Remote Desktop
* TeamViewer
* VNC

---

## Setup Overview

You will install NoMachine on:

### 1) Onboard Computer (Server)

Examples:

* Raspberry Pi
* NVIDIA Jetson
* Linux mini PC
* Ubuntu computer

This will **host** NoMachine.

---

### 2) Your Computer (Client)

Examples:

* MacBook
* Windows
* Linux

This will **connect** to the onboard computer.

---

# Step 1: Install NoMachine on the Onboard Computer

## On Raspberry Pi / Jetson / Linux

### 1. Go to NoMachine website

Download from:

[https://www.nomachine.com/download](https://www.nomachine.com/download)

---

### 2. Select the right version for your system

**Might be under `Embedded Editions`**

Click on:

**ARM** (for Jetson)

or

**Raspberry Pi**

or

**Linux x86** (for Ubuntu PC)

Download the `.deb` file.

Example:

```
nomachine_8.x.x_arm64.deb
```

---

### 3. Install NoMachine

Open terminal on the onboard computer and run:

```bash
cd Downloads
sudo dpkg -i nomachine*.deb
sudo apt install -f
```

---

### 4. Start NoMachine

It usually starts automatically.

To verify:

```bash
sudo /etc/NX/nxserver --status
```

You should see:

```
NX> 162 Enabled service: nxserver.
NX> 162 NX service is running.
```

---

# Step 2: Find the Onboard Computer IP Address

You need the onboard computer’s IP address to connect.

Run:

```bash
hostname -I
```

Example output:

```
192.168.1.45
```

---

# Step 3: Install NoMachine on Your Computer (Client)

## Mac

Download:

macOS version

Install like a normal app.

---

## Windows

Download:

Windows version

Run installer.

---

## Linux

Download:

Linux version

Install with:

```bash
sudo dpkg -i nomachine*.deb
```

---

# Step 4: Connect to the Onboard Computer

## Open NoMachine

Launch NoMachine.

You will see:

```
Add or New
Search
```

If your laptop and the onboard computer are on the same network, you should see the onboard computer

If you do not see it, you can try manually adding it as follow:

Click:

**Add/New**

---

## Enter onboard computer IP

Ex: in Host section, Enter

```
192.168.1.45
```

Protocol:

```
NX
```

After configuring the connection, you should be able to connect

## Connect

Login using:

Onboard computer username & password

---

## You should be able to see the robot desktop

You now have full remote control.

---

# Headless Setup (No Monitor Connected)

> **IMPORTANT**

Some single-board computers **do not start the desktop without a monitor**.

### Fix

You can use a dummy HDMI connector for that or enable virtual display.

---

# Enable auto login (Optional)

Make sure desktop starts automatically.

Check:

```bash
systemctl get-default
```

Should be:

```
graphical.target
```

If not:

```bash
sudo systemctl set-default graphical.target
```

Then:

```bash
sudo nano /etc/gdm3/custom.conf
```

Uncomment:

```
AutomaticLoginEnable = true
AutomaticLogin = ubuntu
```

Reboot.

---

# Set Static IP (Optional)

Use Router to set the onboard computer IP Address to static so the IP never changes.

Example:

```
192.168.1.45
```

This avoids connection issues.

---

# Connecting Over Wi-Fi Hotspot

Common setup:

Onboard Computer creates hotspot.

Laptop connects to Onboard Computer.

Then NoMachine connects.

Example:

Onboard Computer:

```
192.168.8.1
```

Laptop connects to:

```
Robot_WiFi
```

Then connect to:

```
192.168.8.1
```

---

# Common Problems

## Cannot Connect

Check:

```bash
sudo /etc/NX/nxserver --status
```

Restart:

```bash
sudo /etc/NX/nxserver --restart
```

---

## Connection Refused

Check firewall:

```bash
sudo ufw disable
```

or open port:

```
4000
```

---
