# Hokuyo 10LX Ethernet Connection Setup

> **Note**  
> If you have a 30LX or a LiDAR that connects via USB, you can skip this section.

---

## Equipment Required

* Fully built vehicle with a Hokuyo 10LX LiDAR
* Pit/Host Laptop **OR**
* External monitor/display, HDMI cable, keyboard, mouse

---

## Connect to Onboard Computer

Connect to the **onboard computer** using either:

* SSH, or
* Monitor + keyboard + mouse (direct connection)

---

## Configure Ethernet (eth0)

To use the Hokuyo 10LX, configure the Ethernet interface on Ubuntu.

**Default LiDAR IP:**

```
192.168.0.10
```

**Subnet:** `255.255.255.0`

---

## Steps (Ubuntu GUI Method)

### 1. Open Network Settings

Click:

```
Settings → Network
```

or run:

```bash
nm-connection-editor
```

---

### 2. Select Wired Connection

Under or next to **Wired or Ethernet**, click the `+` or gear icon.

---

### 3. Go to IPv4 Settings

Select the **IPv4** tab.

Change:

```
Method → Manual
```

---

### 4. Enter Network Information

Add the following:

| Field   | Value         |
| ------- | ------------- |
| Address | 192.168.0.15  |
| Netmask | 255.255.255.0 |
| Gateway | 192.168.0.10  |

DNS can be left empty.

---

### 5. Rename Connection

Change the connection name or device name to:

```
Hokuyo
```

Click:

```
Save or Add
```

---

## Connect and Verify

### 1. Plug in Hokuyo 10LX

Connect the Ethernet cable from the LiDAR to the Jetson NX.

---

### 2. Enable Hokuyo Network

In:

```
Settings → Network
```

Ensure **Hokuyo (Wired)** is active (It will show that a wired connection is connected).

---

### 3. Test Connection

Run:

```bash
ping 192.168.0.10
```

Expected output:

```
64 bytes from 192.168.0.10: icmp_seq=1 ttl=64 time=...
```

This confirms the LiDAR is reachable.

---

## Optional (Terminal Method)

If GUI is not available:

```bash
sudo nmcli con add type ethernet ifname eth0 con-name Hokuyo ip4 192.168.0.15/24 gw4 192.168.0.10
sudo nmcli con up Hokuyo
```

Test:

```bash
ping 192.168.0.10
```
