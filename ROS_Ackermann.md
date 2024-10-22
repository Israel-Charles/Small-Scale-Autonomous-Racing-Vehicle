
---

## Step 1: Create the Package

Navigate to your ROS 2 workspace (e.g., `~/ros2_ws`) and create a new package named **`drive_com`** with support for Python and dependencies on **`ackermann_msgs`**.

```bash
cd ~/ros2_ws/src
ros2 pkg create drive_com --build-type ament_python --dependencies rclpy ackermann_msgs
```

---

## Step 2: Add Dependencies to `package.xml`

Edit the **`package.xml`** file to declare the **`ackermann_msgs`** dependency properly. This ensures it can be installed via `rosdep`.

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

  <exec_depend>ackermann_msgs</exec_depend>

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

First, make sure all dependencies are installed using **`rosdep`**:

```bash
cd ~/ros2_ws
rosdep install --from-paths src --ignore-src -r -y
```

Now, build the package:

```bash
colcon build --packages-select drive_com
source install/setup.bash
```

---

## Step 6: Test the Nodes

### 6.1: Set ROS Parameters

You can set parameters **`v`** and **`d`** via the command line like this:

```bash
ros2 param set /talker v 2.5
ros2 param set /talker d 1.2
```

Alternatively, you can create a YAML file (e.g., `talker_params.yaml`):
```yaml
talker:
  ros__parameters:
    v: 2.5
    d: 1.2
```

And load it using:
```bash
ros2 run drive_com talker --ros-args --params-file talker_params.yaml
```

---

### 6.2: Run the Nodes

Open two terminals and run the **`talker`** and **`relay`** nodes.

**Terminal 1: Run Talker Node**
```bash
source ~/ros2_ws/install/setup.bash
ros2 run drive_com talker
```

**Terminal 2: Run Relay Node**
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
