Below is a step-by-step guide to **create both a Python and C++ package** in a **ROS 2 Foxy workspace**.

---

## **Step 1: Create a ROS 2 Workspace**

Open a terminal and create a new workspace:

```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws
```

Then, **initialize the workspace**:

```bash
colcon build
```

---

## **Step 2: Create a Python Package**

Navigate to the `src/` directory:

```bash
cd ~/ros2_ws/src
```

Now, create a Python package named **`my_python_pkg`**:

```bash
ros2 pkg create my_python_pkg --build-type ament_python --dependencies rclpy
```

This creates the following structure:

```
my_python_pkg/
├── package.xml
├── setup.py
├── resource/
│   └── my_python_pkg
└── my_python_pkg/
    └── __init__.py
```

### **Modify `setup.py` to Install the Python Code**

Edit the `setup.py` file:

```bash
nano my_python_pkg/setup.py
```

Add the following to ensure the Python module installs correctly:

```python
from setuptools import setup

package_name = 'my_python_pkg'

setup(
    name=package_name,
    version='0.1.0',
    packages=[package_name],
    install_requires=['setuptools'],
    zip_safe=True,
    maintainer='Your Name',
    maintainer_email='your.email@example.com',
    description='A simple Python ROS2 package',
    license='Apache License 2.0',
    entry_points={
        'console_scripts': [
            'talker = my_python_pkg.talker:main',
        ],
    },
)
```

### **Add a Basic Talker Node**

Create a Python node inside `my_python_pkg`:

```bash
nano my_python_pkg/talker.py
```

Add the following code to publish a message periodically:

```python
import rclpy
from rclpy.node import Node
from std_msgs.msg import String

class Talker(Node):
    def __init__(self):
        super().__init__('talker')
        self.publisher_ = self.create_publisher(String, 'chatter', 10)
        self.timer = self.create_timer(0.5, self.timer_callback)

    def timer_callback(self):
        msg = String()
        msg.data = 'Hello ROS 2 Foxy'
        self.publisher_.publish(msg)
        self.get_logger().info(f'Publishing: "{msg.data}"')

def main(args=None):
    rclpy.init(args=args)
    node = Talker()
    try:
        rclpy.spin(node)
    except KeyboardInterrupt:
        pass
    node.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```

---

## **Step 3: Create a C++ Package**

Navigate to the `src/` directory:

```bash
cd ~/ros2_ws/src
```

Create a C++ package named **`my_cpp_pkg`**:

```bash
ros2 pkg create my_cpp_pkg --build-type ament_cmake --dependencies rclcpp std_msgs
```

This creates the following structure:

```
my_cpp_pkg/
├── CMakeLists.txt
├── package.xml
└── src/
```

### **Add a Basic Talker Node in C++**

Create the C++ talker node:

```bash
nano my_cpp_pkg/src/talker.cpp
```

Add the following code:

```cpp
#include <chrono>
#include <memory>
#include "rclcpp/rclcpp.hpp"
#include "std_msgs/msg/string.hpp"

using namespace std::chrono_literals;

class Talker : public rclcpp::Node {
public:
    Talker() : Node("talker") {
        publisher_ = this->create_publisher<std_msgs::msg::String>("chatter", 10);
        timer_ = this->create_wall_timer(500ms, std::bind(&Talker::timer_callback, this));
    }

private:
    void timer_callback() {
        auto message = std_msgs::msg::String();
        message.data = "Hello ROS 2 Foxy";
        RCLCPP_INFO(this->get_logger(), "Publishing: '%s'", message.data.c_str());
        publisher_->publish(message);
    }

    rclcpp::Publisher<std_msgs::msg::String>::SharedPtr publisher_;
    rclcpp::TimerBase::SharedPtr timer_;
};

int main(int argc, char * argv[]) {
    rclcpp::init(argc, argv);
    rclcpp::spin(std::make_shared<Talker>());
    rclcpp::shutdown();
    return 0;
}
```

### **Modify `CMakeLists.txt` to Build the Node**

Edit `CMakeLists.txt`:

```bash
nano my_cpp_pkg/CMakeLists.txt
```

Make sure it contains:

```cmake
cmake_minimum_required(VERSION 3.5)
project(my_cpp_pkg)

find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)

add_executable(talker src/talker.cpp)
ament_target_dependencies(talker rclcpp std_msgs)

install(TARGETS
  talker
  DESTINATION lib/${PROJECT_NAME}
)

ament_package()
```

---

## **Step 4: Build the Workspace**

Go to the root of your workspace and build everything using `colcon`:

```bash
cd ~/ros2_ws
colcon build
```

After the build completes, **source the workspace**:

```bash
source install/setup.bash
```

---

## **Step 5: Run the Nodes**

### **Run the Python Node:**
```bash
ros2 run my_python_pkg talker
```

### **Run the C++ Node:**
```bash
ros2 run my_cpp_pkg talker
```

---

## **Optional: Check Topics**

To verify the nodes are publishing to the `chatter` topic:

```bash
ros2 topic echo /chatter
```

---

This setup provides a **working ROS 2 workspace** with both a **Python and a C++ package**. You can now build upon these nodes or add subscribers to complete the system!
