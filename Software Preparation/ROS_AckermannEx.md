# Ackermann Steering in ROS

Instructions to create create nodes that publish and subscribe to AckermannDriveStamped messages

## Background Information

> Ackermann steering is a common steering mechanism used in many vehicles, particularly those designed for on-road driving, such as cars and trucks. The fundamental principle of Ackermann steering is to allow the wheels to follow a circular path during a turn, ensuring that the inner wheel rotates at a different speed than the outer wheel. This system helps in reducing tire wear and improves vehicle handling.
>
> In the context of Robot Operating System (ROS), Ackermann drive models are often employed in mobile robotics, particularly for autonomous vehicles. In ROS, you can utilize specific message types and packages designed to handle Ackermann-style steering, making it easier to control robot movement effectively.
>
> In ROS, you can use the ackermann_msgs package, which provides message types for controlling vehicles with an Ackermann steering mechanism. The primary message type is AckermannDriveStamped, which includes:
>
> **drive:**
>
> - speed: The linear speed of the vehicle.
> - steering_angle: The angle of the steering in radians.
>
> Here’s a brief overview of the message structure:
>
> ```bash
> # AckermannDrive message
> # This represents the drive control signals for an Ackermann-steering vehicle
> float64 speed       # Speed in meters per second
> float64 steering_angle  # Steering angle in radians
>
> # AckermannDriveStamped message
> # This represents the AckermannDrive message with a timestamp
> std_msgs/Header header  # Standard message header (timestamp, frame_id)
> AckermannDrive drive     # The drive command (speed, steering_angle)
> ```

---

## Step 1: Create the Package

Navigate to your ROS 2 workspace (e.g., `~/ros2_ws`) and create a new package named **`drive_com`** with support for Python and dependencies on **`ackermann_msgs`**.

```bash
cd ~/ros2_ws/src
ros2 pkg create drive_com --build-type ament_python --dependencies rclpy ackermann_msgs
```

---

## Step 2: Ensure the Dependencies to `package.xml` are correct

Make sure the **`package.xml`** file declare the **`ackermann_msgs`** dependency properly. This ensures it can be installed via `rosdep`.

**File:** `~/ros2_ws/src/drive_com/package.xml`

```xml
<?xml version="1.0"?>
<package format="3">
  <name>drive_com</name>
  <version>0.0.1</version>
  <description>A ROS 2 package to publish and relay AckermannDriveStamped messages.</description>

  <maintainer email="your_email@example.com">Your Name</maintainer>
  <license>Apache-2.0</license>

  <buildtool_depend>ament_python</buildtool_depend>

  <depend>rclpy</depend>
  <depend>ackermann_msgs</depend>

  <export>
    <build_type>ament_python</build_type>
  </export>
</package>
```

---

## Step 3: Define Python Nodes

### 3.1: Create the Talker Node

Create a new Python file named **`talker.py`** inside the `drive_com/drive_com` directory.

**File:** `~/ros2_ws/src/drive_com/drive_com/talker.py`

```python
import rclpy
from rclpy.node import Node
from ackermann_msgs.msg import AckermannDriveStamped

class Talker(Node):
    def __init__(self):
        super().__init__('talker')

        # Declare parameters 'v' and 'd' with default values
        # Note that parameters are dynamic values that can be changed within this code or externally by ROS, unlike regular python variables that are only internal
        self.declare_parameter('v', 0.0)
        self.declare_parameter('d', 0.0)

        # Create a publisher for the 'drive' topic
        self.publisher_ = self.create_publisher(AckermannDriveStamped, 'drive', 10)

        # Publish messages as fast as possible
        self.timer = self.create_timer(0.01, self.publish_drive_message)

    def publish_drive_message(self):
        # Get the parameters 'v' and 'd'
        v = self.get_parameter('v').get_parameter_value().double_value
        d = self.get_parameter('d').get_parameter_value().double_value

        # Create and publish an AckermannDriveStamped message
        msg = AckermannDriveStamped()
        msg.drive.speed = v
        msg.drive.steering_angle = d

        self.publisher_.publish(msg)
        self.get_logger().info(f'Publishing: speed={v}, steering_angle={d}')

def main(args=None):
    rclpy.init(args=args)
    node = Talker()

    try:
        rclpy.spin(node)
    except KeyboardInterrupt:
        node.get_logger().info('Talker stopped by user.')
    finally:
        node.destroy_node()
        rclpy.shutdown()

if __name__ == '__main__':
    main()
```

---

### 3.2: Create the Relay Node

Create another Python file named **`relay.py`** inside the `drive_com/drive_com` directory.

**File:** `~/ros2_ws/src/drive_com/drive_com/relay.py`

```python
import rclpy
from rclpy.node import Node
from ackermann_msgs.msg import AckermannDriveStamped

class Relay(Node):
    def __init__(self):
        super().__init__('relay')

        # Create a subscriber for the 'drive' topic
        self.subscription = self.create_subscription(
            AckermannDriveStamped,
            'drive',
            self.listener_callback,
            10
        )

        # Create a publisher for the 'drive_relay' topic
        self.publisher_ = self.create_publisher(AckermannDriveStamped, 'drive_relay', 10)

    def listener_callback(self, msg):
        # Multiply the speed and steering_angle by 3
        new_msg = AckermannDriveStamped()
        new_msg.drive.speed = msg.drive.speed * 3
        new_msg.drive.steering_angle = msg.drive.steering_angle * 3

        # Publish the new message
        self.publisher_.publish(new_msg)
        self.get_logger().info(f'Relaying: speed={new_msg.drive.speed}, steering_angle={new_msg.drive.steering_angle}')

def main(args=None):
    rclpy.init(args=args)
    node = Relay()

    try:
        rclpy.spin(node)
    except KeyboardInterrupt:
        node.get_logger().info('Relay stopped by user.')
    finally:
        node.destroy_node()
        rclpy.shutdown()

if __name__ == '__main__':
    main()
```

---

## Step 4: Update `setup.py`

Update the **`setup.py`** file to include both nodes.

**File:** `~/ros2_ws/src/drive_com/setup.py`

```python
from setuptools import setup

package_name = 'drive_com'

setup(
    name=package_name,
    version='0.0.1',
    packages=[package_name],
    install_requires=['setuptools'],
    zip_safe=True,
    maintainer='your_name',
    maintainer_email='your_email@example.com',
    description='A ROS 2 package for drive control communication',
    license='Apache-2.0',
    tests_require=['pytest'],
    entry_points={
        'console_scripts': [
            'talker = drive_com.talker:main',
            'relay = drive_com.relay:main',
        ],
    },
)
```

---

## Step 5: Build and Install Dependencies

> If new dependencies were added or used, make sure they are properly declared in the `package.xml` file. Then, make sure all dependencies are installed using **`rosdep`**:
>
> ```bash
> cd ~/ros2_ws
> rosdep install --from-paths src --ignore-src -r -y
> ```
>
>> `--ignore-src -r -y` means that:
>>
>> - It ignores dependencies that are already present in the source code.
>> - It installs missing system dependencies using the package manager (apt for Ubuntu).
>> - It reinstalls any system dependencies if needed (-r).
>> - It runs without asking for confirmation (-y).

Now, build the package:
> **Make sure that you are at the workspace root folder**

```bash
colcon build --packages-select drive_com
source install/setup.bash
```

---

## Step 6: Test the Nodes

### 6.1: Set ROS Parameters

Nodes can be run directly with parameters by using the `--ros-args -p`. For example:

```bash
ros2 run drive_com talker --ros-args -p v:=2.1 -p d:=1.4
```

When the node is running, parameters can be set, ex: **`v`** and **`d`**, via the command line like this:

```bash
ros2 param set /talker v 2.5
ros2 param set /talker d 1.2
```

Alternatively, a YAML file can be created (e.g., `talker_params.yaml`):

```yaml
talker:
  ros__parameters:
    v: 2.5
    d: 1.2
```

And load by using the the option `--ros-args --params-file <address of the yaml file>`. For example (Given that you are in the workspace top level folder and the parameter file is in the package, inside a folder called `config`):

```bash
ros2 run drive_com talker --ros-args --params-file src/drive_com/config/talker_params.yaml
```

---

### 6.2: Run the Nodes

Open two terminals and run the **`talker`** and **`relay`** nodes.

#### Terminal 1: Run Talker Node

```bash
source ~/ros2_ws/install/setup.bash
ros2 run drive_com talker
```

#### Terminal 2: Run Relay Node

```bash
source ~/ros2_ws/install/setup.bash
ros2 run drive_com relay
```

---

### 6.3: Monitor Topics

You can inspect the messages being published on the **`drive`** and **`drive_relay`** topics:

```bash
ros2 topic echo /drive
ros2 topic echo /drive_relay
```

---

## Launch File

To create a **ROS 2 launch file** that launches both the **`talker`** and **`relay`** nodes, while also setting parameters for the **`talker`**, follow these steps:

---

### Step 1: Create a `launch/` Directory

Navigate to the `drive_com` package and create a `launch/` directory if it doesn’t already exist.

```bash
cd ~/ros2_ws/src/drive_com
mkdir -p launch
```

---

### Step 2: Create the YAML Parameter File

Place the **YAML parameter file** in the `config/` directory.

**File:** `~/ros2_ws/src/drive_com/config/talker_params.yaml`

```yaml
talker:
  ros__parameters:
    v: 2.5
    d: 1.2
```

---

### Step 3: Create the Launch File

Create a launch file that launches **both `talker` and `relay` nodes** and sets parameters for the `talker` node using the YAML file.

**File:** `~/ros2_ws/src/drive_com/launch/drive_com_launch.py`

```python
from launch import LaunchDescription
from launch_ros.actions import Node

def generate_launch_description():
    # Define the talker node with its parameters
    # Make sure to use absolute path for any file path
    talker_node = Node(
        package='drive_com',
        executable='talker',
        name='talker',
        parameters=['<absolute path to file ex: `/home/israel/ros2_ws/src/drive_com/config/talker_params.yaml`>'],
        output='screen'
    )

    # Define the relay node
    relay_node = Node(
        package='drive_com',
        executable='relay',
        name='relay',
        output='screen'
    )

    # Launch both nodes
    return LaunchDescription([
        talker_node,
        relay_node
    ])
```

The parameters can also be directly set in the launch file by doing something like this

```python
# Define the parameters directly in the launch file
    talker_params = {
        'v': 12.15,
        'd': 12.2
    }

    # Define the talker node with its parameters
    talker_node = Node(
        package='drive_com',
        executable='talker',
        name='talker',
        parameters=[talker_params],
        output='screen'
    )
```

---

### Step 4: Build the Package

Make sure your package is properly built.

```bash
cd ~/ros2_ws
colcon build --packages-select drive_com
source install/setup.bash
```

---

### Step 5: Run the Launch File

Run the launch file, by using `ros2 launch <address of launch file>` to start both the **talker** and **relay** nodes, and set the **parameters** for the talker node. For example, while in the worskspace top level directory:

```bash
ros2 launch src/drive_com/launch/drive_com_launch.py
```

---

### Step 6: Verify the Setup

You can verify that the nodes are running and the parameters are correctly set:

#### List Running Nodes

```bash
ros2 node list
```

You should see:

```bash
/talker
/relay
```

#### Check the Parameters for Talker

```bash
ros2 param list /talker
ros2 param get /talker v
ros2 param get /talker d
```

#### Monitor Topics

Check if messages are being published correctly on the **`drive`** and **`drive_relay`** topics:

```bash
ros2 topic echo /drive
ros2 topic echo /drive_relay
```

---

### Summary

This launch file ensures:

1. **Both nodes (`talker` and `relay`) are launched.**
2. **Parameters for `talker`** are set via the **YAML file**.
3. **Output from both nodes** is shown on the screen.

---
