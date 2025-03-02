# F1TENTH Gym ROS for Ubuntu 20.04 & Ubuntu 22.04

This guideline will walk you through installing the F1TENTH Gym environment (a Python-based simulation library) and integrating the ROS2 communication bridge. You can follow these steps if you are running Ubuntu 20.04 or Ubuntu 22.04 natively or through a virtual machine.

---

## 1. Update Your System and Install Basic Dependencies

Before you start, make sure your system packages are up-to-date and that Python3’s pip is installed:

```bash
sudo apt update && sudo apt upgrade
sudo apt install python3-pip
```

---

## 2. Clone and Install F1TENTH Gym

Change to your home directory (or any preferred location) and clone the F1TENTH Gym repository:

```bash
cd $HOME
git clone https://github.com/f1tenth/f1tenth_gym
cd f1tenth_gym && pip3 install -e .
```

> *Tip:* Installing in “editable” mode (`-e`) allows you to update the package locally if needed.

---

## 3. Set Up Your ROS2 Workspace

Create a new workspace directory for your ROS2 simulation:

```bash
cd $HOME
mkdir -p sim_ws/src
```

Next, navigate into the `src` folder and clone the ROS2 bridge repository:

```bash
cd $HOME/sim_ws/src
git clone https://github.com/f1tenth/f1tenth_gym_ros
```

---

### 4. Update the Simulation Configuration

The simulation uses a YAML configuration file to set parameters such as the map file path. Open the file `sim.yaml` (found under `f1tenth_gym_ros/config/`) in your favorite editor:

```bash
nano $HOME/sim_ws/src/f1tenth_gym_ros/config/sim.yaml
```

Locate the `map_path` parameter and update its value to reflect the correct path on your system. For example, change it to:

```bash
map_path: "<your_home_dir>/sim_ws/src/f1tenth_gym_ros/maps/levine"
```

Replace `<your_home_dir>` with the absolute path to your home directory.

---

### 5. Initialize rosdep and Install ROS2 Dependencies

First, initialize `rosdep` (if you haven’t already):

For Ubuntu 20.04, use:

```bash
sudo rosdep init
rosdep update
```

For Ubuntu 22.04, use:
  
```bash
sudo rosdep init
rosdep update --include-eol-distros
```

Next, move to the workspace’s top-level directory and install the package dependencies. The command differs depending on your ROS2 distro:

- **For Ubuntu 20.04 (ROS2 Foxy):**

  ```bash
  cd $HOME/sim_ws
  rosdep install -i --from-path src --rosdistro foxy -y
  ```

- **For Ubuntu 22.04 (ROS2 Humble):**

  ```bash
  cd $HOME/sim_ws
  rosdep install -i --from-path src --rosdistro humble -y
  ```

> *Tip:* Always ensure you have sourced your ROS2 installation (e.g., `source /opt/ros/foxy/setup.bash` or `source /opt/ros/humble/setup.bash`) before running rosdep commands.

---

### 6. Build the ROS2 Workspace

Once all dependencies are installed, build the workspace using `colcon`:

```bash
colcon build
```

---

### 7. Launching the Simulation

After the build completes, you can launch the simulation. Open a new terminal (or use a multiplexer like `tmux`), and make sure to source both your ROS2 setup and the local workspace setup:

**For Ubuntu 20.04 (ROS2 Foxy):**

```bash
source /opt/ros/foxy/setup.bash        # or humble, if applicable
source $HOME/sim_ws/install/local_setup.bash
```

**For Ubuntu 22.04 (ROS2 Humble):**

```bash
source /opt/ros/humble/setup.bash        # or humble, if applicable
source $HOME/sim_ws/install/local_setup.bash
```

Now, launch the ROS2 simulation bridge:

```bash
ros2 launch f1tenth_gym_ros gym_bridge_launch.py
```

If everything is set up correctly, you should see a simulation window (or RViz visualization) pop up displaying the simulation environment.

---

### 8. Running Additional Nodes

Since the containerized environment includes `tmux` (or you can simply open new terminals), you can run additional ROS2 nodes or interact with the simulation as needed. For example, to open a new session with `tmux`:

```bash
tmux new -s additional_session
```

Then, run any ROS2 command or node from the new session.

---
