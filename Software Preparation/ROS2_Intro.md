# ROS 2 Basics for Beginners

## What is ROS 2?

ROS 2 (Robot Operating System 2) is a flexible framework for developing robot software. It provides tools, libraries, and conventions to simplify the creation of complex, robust robot applications. ROS 2 builds upon the original ROS framework, incorporating significant improvements in performance, flexibility, real-time capabilities, and support for multi-robot systems.

---

## Key Concepts in ROS 2

### 1. Nodes
- **Definition**: A node is a basic unit of computation in ROS 2, implemented as a process that performs specific tasks.
- **Example**: One node might handle sensor data, while another manages motor commands.

### 2. Topics
- **Definition**: Named channels for nodes to exchange messages asynchronously. Nodes can publish messages to topics or subscribe to receive messages.
- **Example**: A sensor node might publish data to `/camera/image_raw`, which another node processes.

### 3. Messages
- **Definition**: Structured data sent over topics, defined using `.msg` files, and capable of including various data types.
- **Example**: A message might contain robot position data or images from a camera.

### 4. Services
- **Definition**: Synchronous communication mechanism where one node sends a request and receives a response.
- **Example**: A service can retrieve a robot’s current position, returning the coordinates as the response.

### 5. Actions
- **Definition**: For long-running tasks that provide intermediate feedback and a final result, extending the request-response model.
- **Example**: Moving a robot arm to a position provides feedback during movement and a result when completed.

### 6. Parameters
- **Definition**: Configurable values that nodes use to modify behavior at runtime without changing code.
- **Example**: A parameter might adjust a robot's speed dynamically.

### 7. Launch Files
- **Definition**: Files for starting multiple nodes and configuring parameters, enabling organized management of complex systems.
- **Example**: A launch file can initialize all nodes for a navigation system.

---

## ROS 2 Structure

### 1. Workspace
- **Definition**: A directory for building and managing ROS 2 projects.
- **Structure**:
  - `src/`: Source code for packages.
  - `install/`: Compiled binaries and libraries.
  - `build/`: Intermediate build files.
  - `log/`: Runtime logs.

### 2. Packages
- **Definition**: Fundamental units of organization in ROS 2, containing nodes, libraries, and resources.
- **Structure**:
  - `package.xml`: Package metadata (e.g., name, dependencies).
  - `CMakeLists.txt`: Build configuration.
  - `src/`: Source code for nodes and libraries.
  - `launch/`: Launch files.
  - `msg/` and `srv/`: Message and service definitions.

### 3. Build System
- **Tool**: `colcon`, used to build packages, handle dependencies, and manage the workspace.

### 4. Communication
- **Middleware**: DDS (Data Distribution Service) handles node discovery, data exchange, and QoS (Quality of Service) settings.

---

## ROS 2 Command-Line

### 1. Node Management
- List active nodes:  
  ```bash
  ros2 node list
  ```
- Get node info:  
  ```bash
  ros2 node info /node_name
  ```

### 2. Topic Management
- List active topics:  
  ```bash
  ros2 topic list
  ```
- Echo topic messages:  
  ```bash
  ros2 topic echo /topic_name
  ```

### 3. Service Management
- List active services:  
  ```bash
  ros2 service list
  ```
- Call a service:  
  ```bash
  ros2 service call /service_name std_srvs/srv/Empty
  ```

### 4. Action Management
- List active actions:  
  ```bash
  ros2 action list
  ```
- Send an action goal:  
  ```bash
  ros2 action send_goal /action_name example_interfaces/action/Fibonacci "{order: 5}"
  ```

### 5. Parameter Management
- List node parameters:  
  ```bash
  ros2 param list /node_name
  ```
- Set a parameter:  
  ```bash
  ros2 param set /node_name param_name value
  ```

### 6. Launch Files
- Launch nodes:  
  ```bash
  ros2 launch package_name launch_file_name.launch.py
  ```
