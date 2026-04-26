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

**For Ubuntu 22.04 and up**

```bash
cd src
git clone --branch humble-devel https://github.com/f1tenth/f1tenth_system.git
```

**For Ubuntu 20.04 and up**
```bash
cd src
git clone https://github.com/f1tenth/f1tenth_system.git
```

### Update Submodules

```bash
cd f1tenth_system
git submodule update --init --force --remote
```

***Replace `humble` with your current ROS distro name***

```bash
sudo apt update
sudo apt install ros-humble-asio-cmake-module
```

### Install Dependencies

**If you had never initialized `rosdep` before, run:*
```bash
sudo rosdep init
```

```bash
cd $HOME/f1tenth_ws
rosdep update
rosdep install --from-paths src -i -y
```

* Might need to run `rosdep update --include-eol-distros`*

### Build Workspace

```bash
colcon build
```

More details:
[https://github.com/f1tenth/f1tenth_system](https://github.com/f1tenth/f1tenth_system)

---

## 4. Launching Teleop and Testing the LiDAR

### Might need to configure LiDAR if IP Adress is not correct in `sensors.yaml` file

Edit:

```
$HOME/f1tenth_ws/src/f1tenth_system/f1tenth_stack/config/sensors.yaml
```

* **Ethernet LiDAR:** set `ip_address` *default is usually fine*
* **USB LiDAR:** comment `ip_address`, set `serial_port` using udev name

---

### Build Workspace

If you changed anything, rebuild

```bash
colcon build
```

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

Sure! Here’s an improved and more detailed version of the explanation:

---

# Controller Configuration for Teleoperation

If you're using a **wireless controller** like a **PS4 controller**, there are a couple of important adjustments to make to the configuration files for both **default** and **human control** to ensure proper functionality.

## 1. **Adjust the Steering Axis for Wireless Controllers**

By default, the **steering angle** is mapped to axis 2 on most controllers. However, for **PS4 controllers** (or similar wireless controllers), the **steering axis** should be mapped to axis 3.

To modify this, change the following line in your configuration:

**For both Default and Human Control**:

Before modification:

```yaml
drive-steering_angle:
          axis: 2
```

After modification:

```yaml
drive-steering_angle:
          axis: 3
```

#### 2. **Adjust the Drive Speed Scale**

For smoother and more manageable control, especially if your car accelerates too quickly, you’ll need to adjust the **drive speed scale**. The default setting (scale: 5.0) might cause your car to accelerate too fast. To avoid this, change the scale from **5.0** to **1.0**.

**Modify the Drive Speed**:

Before modification:

```yaml
drive-speed:
          axis: 1
          scale: 5.0
```

After modification:

```yaml
drive-speed:
          axis: 1
          scale: 1.0
```

This change will make the car’s acceleration much smoother and easier to control, ensuring you don’t get too much speed too quickly.

## 3. **Mapping Joystick Controls**

If during teleoperation the joystick isn't responding as expected, you may need to remap the joystick axes in the `joy_teleop.yaml` file. Here's how to do it:

1. **Locate the Configuration File**:
   The file you need to edit is located at:
   `/f1tenth_ws/src/f1tenth_system/f1tenth_stack/config/joy_teleop.yaml`.

2. **Launch the Bringup and Check the Joy Topic**:
   To identify the joystick mapping, you can launch the **bringup launch** and then **echo the `/joy` topic** to monitor the joystick input.

   Run the following command to echo the `/joy` topic:

   ```bash
   ros2 topic echo /joy
   ```

3. **Move the Joystick**:
   As you move the joystick in different directions, you should see the values change in the echoed message. Pay attention to the indices in the array that change as you move the joystick in each direction. These indices correspond to the axis ID for each joystick movement.

4. **Modify the YAML**:
   Once you've identified the correct indices (axis IDs) for the joystick directions, you’ll need to update the `joy_teleop.yaml` file to reflect these changes under `human_control`.

## 4. **Check for Device Name Mismatches**

If after all this, nothing happens or the joystick still doesn’t work, one possible issue could be a mismatch in the **device name**. The driver might be listening on the wrong port for the joystick.

To troubleshoot, check the `joy_teleop.yaml` file again and verify the `device_name` parameter. The **device name** should match the **udev name** (the name of the joystick device) that you’ve set up earlier. For example, if you’re using a **Logitech joystick**, the name in the config should match the udev name.

To check the **assigned device name**, run the following command:

```bash
ls /dev/input/*
```

It will list devices like:

```
/dev/input/js0
```

If you're using a joystick that doesn’t have udev rules set up, the name should follow the format `/dev/input/js*`.

## 5. **Dead Man's Switch (R1/RB Button)**

Note that for autonomous node to run, you need to press **R1/RB button**.

A **safety feature** you should be aware of is the **R1/RB button** on the joystick, which acts as a **“dead man’s switch”**. If the car gets out of control or you need to stop it quickly, releasing the R1/RB button will immediately stop the car.

## 6. **Rebuild After Modifying Controller Settings**

Once you’ve made the changes to the controller configuration and joystick mappings, don’t forget to rebuild the system. Run the following commands to rebuild the system:

```bash
colcon build --packages-select f1tenth_stack
source install/setup.bash
```

---

