# Simple ROS 2 Subscriber in C++

A simple ROS 2 node which subscribes to messages of the topic `ros2_simple_cpp`.

## Prerequisites

* `ros-base` snap. The base snap which provides the ROS 2 runtime binaries. Has to be installed on ctrlX OS. See [ROS 2 Humble Base Snap](../ros2-base-humble-deb/README.md).
* An Ubuntu based build environment to build an app. See [ctrlX Automation SDK](https://github.com/boschrexroth/ctrlx-automation-sdk).

## Basis for this Project

This project is based on the official ROS 2 Tutorial: [Writing a simple publisher and subscriber (C++)](https://docs.ros.org/en/humble/Tutorials/Beginner-Client-Libraries/Writing-A-Simple-Cpp-Publisher-And-Subscriber.html#writing-a-simple-publisher-and-subscriber-c).

## Building a Snap

Building a snap has two steps:

1. Colcon build: `CMakeLists.txt` defines how compile and link the C++ sources.
2. snap build: `snap/snapcraft.yaml` defines how the compiled binaries are packed into the snap and how they are called on ctrlX OS.

### Colcon Configuration

The colcon build tool is configured by `CMakeLists.txt`.

This section defines the ROS 2 packages needed:

    find_package(ament_cmake REQUIRED)
    find_package(rclcpp REQUIRED)
    find_package(std_msgs REQUIRED)

And here the executables and their dependencies are defined:

    add_executable(listener src/subscriber_member_function.cpp)
    ament_target_dependencies(listener rclcpp std_msgs)

### Snapcraft Configuration

`snap/snapcraft.yaml` defines how the snap will be build:

* `install/` is dumped into the snap
* also `wrapper/`
* Two apps (talker and listener) are copied into the snap and started as services
* The snap - respectively the executables - uses the content interface of the `ros-base` snap (here the ROS 2 runtime is provided).

### Build the Snap

Start this script:

    ./build-snap-amd64.sh

## About

SPDX-FileCopyrightText: Copyright (c) 2023 Bosch Rexroth AG

<https://www.boschrexroth.com/en/dc/imprint/>

## Licenses

SPDX-License-Identifier: Apache-2.0


---

# FROM HERE BOSCH CR CUSTOM CHANGES

This snap has been modified to work in  `devmode` also add a bash app, which can be executed manually to open a bash terminal "into the snap".

Furthermore, a script has been added which sources the ROS2 environment properly in this new bash terminal. It can then be used for simple ROS2 introspection.

How to use:
1. Build the app same as before - using the original `build-snap-amd64.sh` script.
1. Copy this snap manually into the CtrlX Core device. See [our wiki](https://github.boschdevcloud.com/bios-robotics/explore-docs/wiki/System-and-Network-Setup#how-to-ssh-into-the-core) on how to SSH or SCP.
1. Open an SSH session into the core, and install this snap manually - ´sudo snap install --devmode ros2-simple-listener-cpp_2.2.x_amd64.snap`
1. Verify if properly installed with `snap list`. The check if everything looks okay with `snap info ros2-simple-listener-cpp`.
1. Check if the interface to the underlay snap `ros2-base-humble` is properly connected:
  * Check curent conenction status via `snap connections ros2-simple-listener-cpp`. This should list the `ros-base` plug, which sould show the Plug interface `ros2-simple-listener-cpp:ros-base` and the Slot interface `ros2-base-humble:ros-base`. If the latter is instead `-`, it means the interface is not connected.
  * You can manually connect it with the command `sudo snap connect ros2-simple-listener-cpp:ros-base ros2-base-humble:ros-base`.
  * Check the connection again and it should be up. If you had to manually connect it, just restart the snap via the CtrlX web interface.
1. Now open a shell into the listener snap by running the newly added app - `ros2-simple-listener-cpp.bash`
1. In the new bash, source the ros2 env by running the new script - `source /snap/ros2-simple-listener-cpp/current/usr/bin/source-env`. It should echo `ROS2 Humble sourced!`.
1. Now you can run CLI ros2 commands. If the talker snap is also running, you can echo its topic. (just retry a couple of time if you dont see the topic)
