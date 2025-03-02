# ROS2 Installation Guide for Ubuntu

Below is a comprehensive ROS2 installation guide for Ubuntu (with support for Foxy, Humble, or Jazzy). This guide also includes instructions for uninstalling ROS2 if needed.

## 1. Enable Ubuntu Repositories

### **Using the Graphical Interface:**

- **Open Software & Updates:**
  - Click on **Activities** and search for **Software & Updates**.
  - Open the **Software & Updates** application.
- **Enable Repositories:**
  - Under the **Ubuntu Software** tab, check the boxes for:
    - **Main:** Free and open-source software supported by Canonical.
    - **Restricted:** Proprietary drivers and firmware.
    - **Universe:** Community-maintained free and open-source software.
    - **Multiverse:** Software that is not free.
- **Update Package List:**
  - Click **Close** and then click **Reload** when prompted to update package information.

### **Using the Command Line:**

1. Open a terminal (press **Ctrl + Alt + T**).
2. Run the following commands:

   ```bash
   sudo add-apt-repository main
   sudo add-apt-repository restricted
   sudo add-apt-repository universe
   sudo add-apt-repository multiverse
   ```

3. If you don’t have **software-properties-common** installed, run:

   ```bash
   sudo apt install software-properties-common
   ```

4. Update the package list:

   ```bash
   sudo apt update
   ```

---

## 2. Ensure Locale is Set to UTF-8

A proper UTF-8 locale is crucial for ROS2 tools and scripts to work correctly.

1. Update package lists and install locales:

   ```bash
   sudo apt update && sudo apt install locales
   ```

2. Generate the UTF-8 locales:

   ```bash
   sudo locale-gen en_US en_US.UTF-8
   sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
   export LANG=en_US.UTF-8
   ```

3. (Optional) Perform a system update:

   ```bash
   sudo apt update && sudo apt upgrade
   ```

---

## 3. Install Essential Tools

Install **curl** if it isn’t already installed:

```bash
sudo apt install curl -y
```

---

## 4. Add the ROS2 Repository Key and Source

1. Download and add the ROS2 GPG key:

   ```bash
   sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
   ```

2. Add the ROS2 repository (this command automatically uses your Ubuntu codename):

   ```bash
   echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
   ```

3. Update and upgrade packages:

   ```bash
   sudo apt update && sudo apt upgrade
   ```

---

## 5. Install ROS2

Choose the ROS2 distribution that matches your Ubuntu version:

- **ROS2 Foxy (Ubuntu 20.04):**

  ```bash
  sudo apt install ros-foxy-desktop python3-argcomplete
  ```

- **ROS2 Humble (Ubuntu 22.04):**

  ```bash
  sudo apt install ros-humble-desktop
  ```

- **ROS2 Jazzy (Ubuntu 24.04):**

  ```bash
  sudo apt install ros-jazzy-desktop
  ```

After installing the chosen ROS2 version, you can install additional development tools:

```bash
sudo apt install ros-dev-tools
```

---

## 6. Configure Your Shell Environment

Each time you open a new terminal, you need to source the ROS2 setup script to configure your environment. Replace `<ROS Distribution>` with `foxy`, `humble`, or `jazzy` as appropriate.

> You can find out which shell you are using by running the command `echo $0`. That command shows the shell that was used to start the current session.

- **For Bash:**

  ```bash
  source /opt/ros/<ROS Distribution>/setup.bash
  ```

- **For Zsh:**

  ```bash
  source /opt/ros/<ROS Distribution>/setup.zsh
  ```

- **For Sh:**

  ```bash
  source /opt/ros/<ROS Distribution>/setup.sh
  ```

---

## 7. Uninstalling ROS2

If you need to remove ROS2 or switch to a source-based installation, use the following commands:

- **For ROS2 Foxy (Ubuntu 20.04):**

  ```bash
  sudo apt remove ~nros-foxy-* && sudo apt autoremove
  ```

- **For ROS2 Humble (Ubuntu 22.04):**

  ```bash
  sudo apt remove ~nros-humble-* && sudo apt autoremove
  ```

- **For ROS2 Jazzy (Ubuntu 24.04):**

  ```bash
  sudo apt remove ~nros-jazzy-* && sudo apt autoremove
  ```

Additionally, remove the ROS2 repository:

```bash
sudo rm /etc/apt/sources.list.d/ros2.list
sudo apt update
sudo apt autoremove
```

(Optional: Consider upgrading packages that were previously shadowed by ROS2 installations.)

```bash
sudo apt upgrade
```
