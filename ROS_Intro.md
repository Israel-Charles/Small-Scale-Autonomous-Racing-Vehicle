# ROS Intro

Instructions for two nodes (Python and C++): a **publisher** that sends string messages and a **subscriber** that receives and prints them.

---

## Prerequisites
Ensure that:
1. Your desired version of ROS is installed.
2. You have sourced the ROS setup file, for example:
   ```bash
   source /opt/ros/foxy/setup.bash
   ```
4. You have a ROS 2 workspace ready. If not, create and source it:
   ```bash
   mkdir -p ~/ros2_ws/src
   cd ~/ros2_ws
   colcon build
   ```

---

## Python

To implement the **publisher and subscriber in C++** transmitting standard `Strings`, follow the steps below. 


### **Step 1: Create a Python Package**

Navigate to the `src/` directory:

```bash
cd ~/ros2_ws/src
```

Create a package named `my_python_pkg` with dependencies on `rclpy` and `std_msgs`:

```bash
ros2 pkg create my_python_pkg --build-type ament_python --dependencies rclpy std_msgs
```

This creates the following structure:

```
my_python_pkg/
├── package.xml # file containing meta information about the package
├── setup.py # containing instructions for how to install the package
├── setup.cfg # required when a package has executables, so ros2 run can find them
├── resource/
│   └── my_python_pkg # marker file for the package
└── my_python_pkg/ # a directory with the same name as your package, used by ROS 2 tools to find your package, contains __init__.py
    └── __init__.py # used to mark directories on disk as Python package directories
```

### Step 2: Write the Publisher Node

In the `my_python_pkg` folder, create the publisher node as `simple_publisher.py`.

**File:** `~/ros2_ws/src/my_python_pkg/my_python_pkg/simple_publisher.py`
```python
import rclpy
from rclpy.node import Node
from std_msgs.msg import String

class SimplePublisher(Node):
    def __init__(self):
        super().__init__('simple_publisher')
        self.publisher_ = self.create_publisher(String, 'topic', 10)
        self.timer = self.create_timer(1.0, self.publish_message)  # 1 second interval
        self.count = 0

    def publish_message(self):
        msg = String()
        msg.data = f'Hello ROS 2: {self.count}'
        self.publisher_.publish(msg)
        self.get_logger().info(f'Publishing: "{msg.data}"')
        self.count += 1

def main(args=None):
    rclpy.init(args=args)
    node = SimplePublisher()
    try:
        rclpy.spin(node)
    except KeyboardInterrupt:
        pass
    finally:
        node.destroy_node()
        rclpy.shutdown()

if __name__ == '__main__':
    main()
```

---

### Step 3: Write the Subscriber Node

Create the subscriber node as `simple_subscriber.py`.

**File:** `~/ros2_ws/src/my_python_pkg/my_python_pkg/simple_subscriber.py`
```python
import rclpy
from rclpy.node import Node
from std_msgs.msg import String

class SimpleSubscriber(Node):
    def __init__(self):
        super().__init__('simple_subscriber')
        self.subscription = self.create_subscription(
            String,
            'topic',
            self.listener_callback,
            10)
        self.subscription  # Prevent unused variable warning

    def listener_callback(self, msg):
        self.get_logger().info(f'Received: "{msg.data}"')

def main(args=None):
    rclpy.init(args=args)
    node = SimpleSubscriber()
    try:
        rclpy.spin(node)
    except KeyboardInterrupt:
        pass
    finally:
        node.destroy_node()
        rclpy.shutdown()

if __name__ == '__main__':
    main()
```

---

### Step 4: Update the Setup Configuration

Modify `setup.py` to install your nodes.

**File:** `~/ros2_ws/src/my_python_pkg/setup.py`
```python
from setuptools import setup

package_name = 'my_python_pkg'

setup(
    name=package_name,
    version='0.0.0',
    packages=[package_name],
    install_requires=['setuptools'],
    zip_safe=True,
    maintainer='your_name',
    maintainer_email='your_email@example.com',
    description='A simple publisher and subscriber example in ROS 2',
    license='Apache License 2.0',
    tests_require=['pytest'],
    entry_points={
        'console_scripts': [
            'simple_publisher = my_python_pkg.simple_publisher:main',
            'simple_subscriber = my_python_pkg.simple_subscriber:main',
        ],
    },
)
```

---

### Step 5: Build and Source the Package

Go to the workspace root, build the package, and source it:
```bash
cd ~/ros2_ws
colcon build
source install/setup.bash
```

---

### Step 6: Run the Nodes

In two separate terminals, run the publisher and subscriber:

**Terminal 1: Publisher**
```bash
source ~/ros2_ws/install/setup.bash
ros2 run my_python_pkg simple_publisher
```

**Terminal 2: Subscriber**
```bash
source ~/ros2_ws/install/setup.bash
ros2 run my_python_pkg simple_subscriber
```

---

### Expected Output

- **Publisher Terminal:**  
  ```
  [INFO] [simple_publisher]: Publishing: "Hello ROS 2: 0"
  [INFO] [simple_publisher]: Publishing: "Hello ROS 2: 1"
  ...
  ```

- **Subscriber Terminal:**  
  ```
  [INFO] [simple_subscriber]: Received: "Hello ROS 2: 0"
  [INFO] [simple_subscriber]: Received: "Hello ROS 2: 1"
  ...
  ```
---

## C++

To implement the **publisher and subscriber in C++** transmitting standard `Strings`, follow the steps below. 

---

### Step 1: Create a C++ Package**

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
├── CMakeLists.txt # file that describes how to build the code within the package
├── include/<package_name> # directory containing the public headers for the package
├── package.xml # file containing meta information about the package
└── src/ # directory containing the source code for the package
```
---

### Step 2: Write the Publisher Node

Create the file `simple_publisher.cpp` inside the `src` directory of the `my_cpp_pkg` package.

**File:** `~/ros2_ws/src/my_cpp_pkg/src/simple_publisher.cpp`

```cpp
#include <rclcpp/rclcpp.hpp>
#include <std_msgs/msg/string.hpp>

class SimplePublisher : public rclcpp::Node {
public:
    SimplePublisher() : Node("simple_publisher"), count_(0) {
        publisher_ = this->create_publisher<std_msgs::msg::String>("topic", 10);
        timer_ = this->create_wall_timer(
            std::chrono::seconds(1),
            std::bind(&SimplePublisher::publish_message, this));
    }

private:
    void publish_message() {
        auto message = std_msgs::msg::String();
        message.data = "Hello ROS 2: " + std::to_string(count_++);
        RCLCPP_INFO(this->get_logger(), "Publishing: '%s'", message.data.c_str());
        publisher_->publish(message);
    }

    rclcpp::Publisher<std_msgs::msg::String>::SharedPtr publisher_;
    rclcpp::TimerBase::SharedPtr timer_;
    size_t count_;
};

int main(int argc, char *argv[]) {
    rclcpp::init(argc, argv);
    rclcpp::spin(std::make_shared<SimplePublisher>());
    rclcpp::shutdown();
    return 0;
}
```

---

### Step 3: Write the Subscriber Node

Create the file `simple_subscriber.cpp` inside the `src` directory of the `my_cpp_pkg` package.

**File:** `~/ros2_ws/src/my_cpp_pkg/src/simple_subscriber.cpp`

```cpp
#include <rclcpp/rclcpp.hpp>
#include <std_msgs/msg/string.hpp>

class SimpleSubscriber : public rclcpp::Node {
public:
    SimpleSubscriber() : Node("simple_subscriber") {
        subscription_ = this->create_subscription<std_msgs::msg::String>(
            "topic", 10, std::bind(&SimpleSubscriber::topic_callback, this, std::placeholders::_1));
    }

private:
    void topic_callback(const std_msgs::msg::String::SharedPtr msg) const {
        RCLCPP_INFO(this->get_logger(), "Received: '%s'", msg->data.c_str());
    }

    rclcpp::Subscription<std_msgs::msg::String>::SharedPtr subscription_;
};

int main(int argc, char *argv[]) {
    rclcpp::init(argc, argv);
    rclcpp::spin(std::make_shared<SimpleSubscriber>());
    rclcpp::shutdown();
    return 0;
}
```

---

### Step 4: Update `CMakeLists.txt`

Modify `CMakeLists.txt` to compile both the publisher and subscriber nodes.

**File:** `~/ros2_ws/src/my_cpp_pkg/CMakeLists.txt`

```cmake
cmake_minimum_required(VERSION 3.5)
project(my_cpp_pkg)

find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)

add_executable(simple_publisher src/simple_publisher.cpp)
ament_target_dependencies(simple_publisher rclcpp std_msgs)

add_executable(simple_subscriber src/simple_subscriber.cpp)
ament_target_dependencies(simple_subscriber rclcpp std_msgs)

install(TARGETS
  simple_publisher
  simple_subscriber
  DESTINATION lib/${PROJECT_NAME})

ament_package()
```

---

### Step 5: Build and Source the Package

Build the package and source it:
```bash
cd ~/ros2_ws
colcon build --packages-select my_cpp_pkg
source install/setup.bash
```

---

### Step 6: Run the Nodes

Open two terminals and run the publisher and subscriber nodes.

**Terminal 1: Run the Publisher**
```bash
source ~/ros2_ws/install/setup.bash
ros2 run my_cpp_pkg simple_publisher
```

**Terminal 2: Run the Subscriber**
```bash
source ~/ros2_ws/install/setup.bash
ros2 run my_cpp_pkg simple_subscriber
```

---

## Expected Output

- **Publisher Terminal:**
  ```
  [INFO] [simple_publisher]: Publishing: 'Hello ROS 2: 0'
  [INFO] [simple_publisher]: Publishing: 'Hello ROS 2: 1'
  ...
  ```

- **Subscriber Terminal:**
  ```
  [INFO] [simple_subscriber]: Received: 'Hello ROS 2: 0'
  [INFO] [simple_subscriber]: Received: 'Hello ROS 2: 1'
  ...
  ```

---


## **Optional: Check Topics**

To verify the nodes are publishing to the `chatter` topic:

```bash
ros2 topic echo /topic
```
