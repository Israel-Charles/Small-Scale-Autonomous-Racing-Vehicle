# Tips and Tricks

While working on this project, you might want to establish some shortcuts to make things easier. You might also run into some common issues. Below are some of the tips and tricks I learned throughout the project.

---

## **Table of Contents**
- [Delay when executing `sudo` commands](#delay-when-executing-sudo-commands)
- [Automatically start ROS Setup file](#automatically-start-ros-setup-file)
- [Basic `tmux` keybindings](#basic-tmux-keybindings)

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
---

## **Automatically start ROS Setup file**

To automatically source the ROS setup file every time you open a terminal, you can add the `source` command to the appropriate shell configuration file. Below are the steps to set this up for different shells like `bash`, `zsh`, or `fish`. 

### **Step 1: Identify Your ROS Version and Shell**
- **ROS Version Example**: Foxy, Humble, Jazzy, or Noetic  
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

### **Step 3: Verify the Setup**
1. Close the terminal and open a new one.  
2. Run a ROS command to confirm the environment is correctly sourced:
   ```bash
   echo $ROS_DISTRO
   ```
   You should see the correct ROS version (e.g., `foxy`).
---

## **Basic `tmux` keybindings**

`tmux` is a helpful that allows you to have multiple bash session in the same terminal window. This will be very convenient working inside containers. It could be installed on your machine or in a container via: `apt update && apt install tmux`.

Here’s a list of some of the **most common `tmux` key bindings** to help you navigate, manage panes, windows, and sessions efficiently. These bindings assume the default **prefix key** is `Ctrl + b` (meaning you first press `Ctrl + b`, release, and then the respective key).

## **Common Tmux Key Bindings**

### **Basic Keybindings**
- **`tmux` + `:`** – Open a tmux session.
- **`tmux new -s <session name>` + `:`** – Open a named tmux session called `<session name>`.
- **`tmux ls`** – List all tmux sessions.
- **`tmux attach`** – Reattach to the most recent detached session.
- **`tmux attach -t <session to attach>`** – Reattach a specific session (`<session to attach>` is the session name).
- **`Ctrl + b` + `d`** – Detach from the current session (session keeps running in the background).
- **`Ctrl + b` + `%`** – Split the window **vertically** (side-by-side panes).
- **`Ctrl + b` + `"`** – Split the window **horizontally** (top-bottom panes).
- **`Ctrl + b` + `<arrow keys>`** – To move between panes (Left, Right, Up, Down).
- **`Ctrl + b` + `x`** – Close the current pane.
- **`Ctrl + b` + `c`** – Create a new window.
- **`Ctrl + b` + `n`** – Switch to the **next** window.
- **`Ctrl + b` + `p`** – Switch to the **previous** window.
- **`Ctrl + b` + `0-9`** – Switch to a specific window by its number.
- **`Ctrl + b` + `w`** – Open a panel to navigate across the sessions and windows of `tmux`.
- **`Ctrl + b` + `?`** – Show the help screen with all key bindings.
- **`Ctrl + b` + `:`** – Open the tmux command prompt.


### **Additional Keybindings**
#### **Window Management**
- **`Ctrl + b` + `,`** – Rename the current window.
- **`Ctrl + b` + `&`** – Close the current window.

#### **Pane Management**
- **`Ctrl + b` + `o`** – Switch to the **next** pane.
- **`Ctrl + b` + `{` / `}`** – Swap the current pane with the previous/next pane.
- **`Ctrl + b` + `z`** – Toggle zoom for the current pane (fullscreen).

#### **Copy Mode**
- **`Ctrl + b` + `[`** – Enter **copy mode** (for scrolling through output).
- **`Ctrl + b` + `]`** – Paste copied content from the buffer.
- **`q`** – Exit copy mode.
- **`Space`** – Begin selection in copy mode.
- **`Enter`** – Copy the selected text to the tmux buffer.

#### **Miscellaneous**
- **`Ctrl + b` + `t`** – Show the current time.

> [!Note]
> **If you want to customize key bindings, you can modify your `~/.tmux.conf` file**

---
