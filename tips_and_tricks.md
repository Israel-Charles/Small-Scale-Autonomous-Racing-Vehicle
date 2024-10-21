# Tips and Tricks

While working on this project, you might want to establish some shortcuts to make things easier. You might also run into some common issues. Below are some of the tips and tricks I learned throughout the project.

---

## **Table of Contents**
- [**Delay when executing `sudo` commands**](#--delay-when-executing--sudo--commands--)
- [**Automatically start ROS Setup file**](#--automatically-start-ros-setup-file--)

---

## **Delay when executing `sudo` commands**
The delay in running `sudo` commands is often related to hostname resolution issues, especially if the hostname of your machine isn’t correctly configured or resolvable. Here are several steps to troubleshoot and resolve this issue on your machine:

### **Steps to Fix Delays in `sudo` Execution**

1. **Check the Hostname Configuration**  
Run the following command to see your current hostname:
   ```bash
   hostname
   ```

   Ensure the output matches what is configured in `/etc/hostname`.

2. **Update `/etc/hostname`**
   Open the `/etc/hostname` file to verify or modify the hostname:
   ```bash
   sudo nano /etc/hostname
   ```

   Make sure it contains a single line with your desired hostname, such as:
   ```
   my-machine
   ```

3. **Update `/etc/hosts`**
   Ensure your hostname is correctly mapped in `/etc/hosts`. Open the file:
   ```bash
   sudo nano /etc/hosts
   ```

   Add a line (or modify if it exists) to look like this:
   ```
   127.0.0.1    localhost
   127.0.1.1    my-machine
   ```

   Replace `my-machine` with the actual hostname you want to use.

   **Note:** The `127.0.1.1` entry should match the hostname from `/etc/hostname`. If it doesn’t, `sudo` may hang while trying to resolve it.

4. ** Might need to Restart Services to Apply Changes**  
   After making the changes, restart the services to apply them:
   ```bash
   sudo systemctl restart systemd-logind
   ```

5. **Verify the Fix**  
   Run a simple `sudo` command to test the response time:
   ```bash
   sudo ls
   ```

   If the command executes quickly, the issue was likely caused by hostname resolution problems.


## **Automatically start ROS Setup file**

To automatically source the ROS setup file every time you open a terminal, you can add the `source` command to the appropriate shell configuration file. Below are the steps to set this up for different shells like `bash`, `zsh`, or `fish`. 

### **Step 1: Identify Your ROS Version and Shell**
- **ROS Version Example**: Foxy, Humble, or Noetic  
- **Shell Example**: `bash`, `zsh`, or `fish`  
- **Setup File Location**: `/opt/ros/<ros_version>/setup.<shell>`

### **Step 2: Add `source` to the Shell Startup File**

1. **For Bash Users:**
   Add the following line to your `~/.bashrc`:
   ```bash
   source /opt/ros/foxy/setup.bash
   ```

   **Command to edit `~/.bashrc`:**
   ```bash
   nano ~/.bashrc
   ```

   After adding the line, reload the file with:
   ```bash
   source ~/.bashrc
   ```

2. **For Zsh Users:**
   Add the following line to your `~/.zshrc`:
   ```bash
   source /opt/ros/foxy/setup.zsh
   ```

   **Command to edit `~/.zshrc`:**
   ```bash
   nano ~/.zshrc
   ```

   After adding the line, reload the file with:
   ```bash
   source ~/.zshrc
   ```

3. **For Fish Users:**
   Add the following line to your `~/.config/fish/config.fish`:
   ```fish
   source /opt/ros/foxy/setup.fish
   ```

   **Command to edit `~/.config/fish/config.fish`:**
   ```bash
   nano ~/.config/fish/config.fish
   ```

   No need to reload, it will apply automatically to new terminals.

---

### **Step 3: Verify the Setup**
1. Close the terminal and open a new one.  
2. Run a ROS command to confirm the environment is correctly sourced:
   ```bash
   echo $ROS_DISTRO
   ```
   You should see the correct ROS version (e.g., `foxy`).

