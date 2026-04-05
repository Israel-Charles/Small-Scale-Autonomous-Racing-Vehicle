# RoboRacer/F1Tenth Driver Stack Setup

## **Equipment Required:**

* Fully built vehicle
* Pit/Host computer **OR**
* External monitor/display, HDMI cable, keyboard, mouse

> ⚠️ **Warning**  
> **Before you proceed**, this section covers how to set up the driver stack **natively** if you have `Ubuntu 20.04` and up, using `ROS 2`.
> For older version of `Ubuntu`, or if you run into issues, use the Docker-based setup instead.

---

## Overview

This section covers:

1. Setting up **udev rules** for sensors
2. Installing **ROS 2 and utilities**
3. Setting up the **driver stack**
4. Launching **teleoperation and LiDAR**

All steps are performed on the **onboard Computer**.

---

## 1. udev Rules Setup

When connecting devices (e.g., VESC, LiDAR), Linux assigns dynamic names like:

```
/dev/ttyACM0
/dev/ttyACM1
```

These change depending on connection order, which is problematic.

### Solution: udev Rules

Assign persistent names like:

```
/dev/sensors/hokuyo
/dev/sensors/vesc
```

### Create Rules

#### Hokuyo

```bash
sudo nano /etc/udev/rules.d/99-hokuyo.rules
```

```bash
KERNEL=="ttyACM[0-9]*", ACTION=="add", ATTRS{idVendor}=="15d1", MODE="0666", GROUP="dialout", SYMLINK+="sensors/hokuyo"
```

#### VESC

```bash
sudo nano /etc/udev/rules.d/99-vesc.rules
```

```bash
KERNEL=="ttyACM[0-9]*", ACTION=="add", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="5740", MODE="0666", GROUP="dialout", SYMLINK+="sensors/vesc"
```

#### Joypad

```bash
sudo nano /etc/udev/rules.d/99-joypad-f710.rules
```

```bash
KERNEL=="js[0-9]*", ACTION=="add", ATTRS{idVendor}=="046d", ATTRS{idProduct}=="c219", SYMLINK+="input/joypad-f710"
```

### Apply Rules

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger
```

Reboot system.

### Verify Devices

```bash
ls /dev/sensors
ls /dev/input
```

### Troubleshoot

If things are not working properly, below are some tools that could help you troubleshoot things.

#### Find Device IDs

```bash
sudo udevadm info --name=<device_name> --attribute-walk
```

#### Real-time device detection

```bash
sudo dmesg -w
```

Then plug in device. You will instantly see what tty they become.

#### Check for errors in your rule or if it would apply

```bash
udevadm test /sys/class/tty/ttyACM0
```

or 

```bash
udevadm test /sys/class/input/js0
```

replace path to the corresponding device you want to test


---

## 2. Installing ROS 2 and Utilities

Follow official guides to install:

- `ROS 2`
- `colcon`
- `rosdep`

---

## 3. Setting up the Driver Stack

### Create Workspace

```bash
cd $HOME
mkdir -p f1tenth_ws/src
```

### Initialize Workspace

```bash
cd f1tenth_ws
colcon build
```

### Clone Repository

```bash
cd src
git clone https://github.com/f1tenth/f1tenth_system.git
```

### Update Submodules

```bash
cd f1tenth_system
git submodule update --init --force --remote
```

### Install Dependencies

```bash
cd $HOME/f1tenth_ws
rosdep update --include-eol-distros
rosdep install --from-paths src -i -y
```

### Build Workspace

```bash
colcon build
```

More details:
[https://github.com/f1tenth/f1tenth_system](https://github.com/f1tenth/f1tenth_system)

---

## 4. Launching Teleop and Testing the LiDAR

### Configure LiDAR

Edit:

```
$HOME/f1tenth_ws/src/f1tenth_system/f1tenth_stack/config/sensors.yaml
```

* **Ethernet LiDAR:** set `ip_address`
* **USB LiDAR:** comment `ip_address`, set `serial_port` using udev name

---

### Source Environment

```bash
source /opt/ros/foxy/setup.bash
cd $HOME/f1tenth_ws
source install/setup.bash
```

---

### Launch System

```bash
ros2 launch f1tenth_stack bringup_launch.py
```

This starts:

* VESC drivers
* LiDAR drivers
* Joystick drivers
* Required system nodes

---

### Visualize LiDAR in RViz

```bash
source /opt/ros/foxy/setup.bash
cd $HOME/f1tenth_ws
source install/setup.bash
rviz2
```

* Add **LaserScan**
* Topic: `/scan`

---

You should now see real-time LiDAR data visualized.

---

If you want, I can also:

* clean this further into a **README.md style**
* convert it into **GitHub docs format with anchors**
* or split it into **multiple markdown files for a docs site**
