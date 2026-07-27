# NIDAR AirMouse Simulation Environment

## Installation Manual

**Target Platform:** Ubuntu 22.04 LTS
**ROS 2:** Humble Hawksbill
**Flight Controller Simulation:** PX4 SITL
**Simulator:** Gazebo Harmonic
**Navigation:** Nav2
**Ground Control Station:** QGroundControl 4.4.5
**Project:** NIDAR AirMouse — Autonomous GPS-Denied Indoor Search, Mapping & Survivor Localisation

---

## 1. Final Simulation Architecture

The desktop simulation will reproduce the architecture of the real AirMouse drone.

```text
┌─────────────────────────────────────────────────────────────┐
│                         UBUNTU 22.04 PC                     │
│                                                             │
│  ┌───────────────────────────────────────────────────────┐  │
│  │                 SIMULATED DRONE                       │  │
│  │                                                       │  │
│  │  ┌─────────────┐       ┌──────────────────────────┐   │  │
│  │  │ Gazebo      │       │ PX4 SITL                 │   │  │
│  │  │             │◄─────►│                          │   │  │
│  │  │ Indoor Maze │       │ Virtual Flight Controller│   │  │
│  │  │ LiDAR       │       │                              │  │
│  │  │ Camera      │       └────────────┬─────────────┘   │  │
│  │  │ Optical Flow│                    │                 │  │
│  │  └──────┬──────┘                    │ MAVLink         │  │
│  │         │                           │                 │  │
│  │         ▼                           ▼                 │  │
│  │  ┌───────────────────────────────────────────────┐    │  │
│  │  │          SIMULATED COMPANION COMPUTER         │    │  │
│  │  │                Raspberry Pi 5                 │    │  │
│  │  │                                               │    │  │
│  │  │  • LiDAR data gateway                         │    │  │
│  │  │  • MAVLink communication                      │    │  │
│  │  │  • Telemetry transmission                     │    │  │
│  │  │  • Command reception                          │    │  │
│  │  │  • Failsafe supervision                       │    │  │
│  │  └───────────────────────┬───────────────────────┘    │  │
│  │                          │                            │  │
│  └──────────────────────────┼────────────────────────────┘  │
│                             │ ROS 2 / MAVLink               │
│                             ▼                               │
│  ┌───────────────────────────────────────────────────────┐  │
│  │                  GROUND STATION                       │  │
│  │                                                       │  │
│  │  • SLAM / Mapping                                     │  │
│  │  • Nav2 Navigation                                    │  │
│  │  • YOLO Survivor Detection                            │  │
│  │  • Survivor Localisation                              │  │
│  │  • Grid Coordinate Calculation                        │  │
│  │  • Live Map                                           │  │
│  │  • Mission Dashboard                                  │  │
│  │  • QGroundControl                                     │  │
│  └───────────────────────────────────────────────────────┘  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

# 2. Software Versions

This installation manual targets:

| Component        | Version                                        |
| ---------------- | ---------------------------------------------- |
| Operating System | Ubuntu 22.04 LTS                               |
| ROS 2            | Humble Hawksbill                               |
| PX4              | Current stable source / pinned project version |
| Gazebo           | Harmonic                                       |
| Nav2             | ROS 2 Humble binary packages                   |
| QGroundControl   | 4.4.5                                          |
| Python           | Ubuntu system Python                           |
| Build System     | colcon                                         |
| Communication    | MAVLink + ROS 2 DDS                            |

ROS 2 Humble is the recommended ROS 2 platform for PX4 on Ubuntu 22.04.

---

# 3. Initial System Update

Open a terminal:

```bash
sudo apt update
sudo apt upgrade -y
```

Install basic development tools:

```bash
sudo apt install -y \
    git \
    curl \
    wget \
    unzip \
    zip \
    build-essential \
    cmake \
    python3-pip \
    python3-venv \
    software-properties-common \
    lsb-release \
    gnupg \
    ca-certificates
```

Restart:

```bash
sudo reboot
```

---

# 4. Install ROS 2 Humble

## 4.1 Configure Locale

```bash
sudo apt update
sudo apt install -y locales

sudo locale-gen en_US en_US.UTF-8

sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8

export LANG=en_US.UTF-8
```

Verify:

```bash
locale
```

You should see:

```text
LANG=en_US.UTF-8
LC_ALL=en_US.UTF-8
```

---

## 4.2 Enable Ubuntu Universe Repository

```bash
sudo apt install -y software-properties-common

sudo add-apt-repository universe

sudo apt update
```

---

## 4.3 Add the ROS 2 Repository

Install the ROS repository key:

```bash
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key \
    -o /usr/share/keyrings/ros-archive-keyring.gpg
```

Add the repository:

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] \
http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" \
| sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
```

Update:

```bash
sudo apt update
```

---

## 4.4 Install ROS 2 Desktop

```bash
sudo apt install -y ros-humble-desktop
```

Install development tools:

```bash
sudo apt install -y \
    ros-dev-tools \
    python3-colcon-common-extensions \
    python3-rosdep
```

The `python3-colcon-common-extensions` package is important because it provides the `colcon build` command used to compile ROS 2 workspaces.

---

## 4.5 Source ROS 2 Automatically

Add ROS 2 to `.bashrc`:

```bash
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
```

Reload:

```bash
source ~/.bashrc
```

Verify:

```bash
ros2 --version
```

Check the environment:

```bash
printenv | grep ROS
```

Expected:

```text
ROS_VERSION=2
ROS_DISTRO=humble
```

---

## 4.6 Test ROS 2

Terminal 1:

```bash
ros2 run demo_nodes_cpp talker
```

Terminal 2:

```bash
ros2 run demo_nodes_py listener
```

If the listener receives messages, ROS 2 is working.

Example:

```text
I heard: [Hello World: 0]
I heard: [Hello World: 1]
I heard: [Hello World: 2]
```

---

# 5. Install rosdep

Initialize rosdep:

```bash
sudo rosdep init
```

If it says that rosdep is already initialized, that is okay.

Update:

```bash
rosdep update
```

Verify:

```bash
rosdep --version
```

---

# 6. Install PX4 SITL and Gazebo Harmonic

PX4's Ubuntu setup script installs the required development toolchain and the recommended Gazebo simulator for Ubuntu 22.04. Current PX4 documentation uses Gazebo Harmonic for Ubuntu 22.04.

## 6.1 Create AirMouse Project Directory

```bash
mkdir -p ~/AirMouse
cd ~/AirMouse
```

---

## 6.2 Clone PX4

```bash
git clone https://github.com/PX4/PX4-Autopilot.git --recursive
```

This creates:

```text
~/AirMouse/
└── PX4-Autopilot/
```

---

## 6.3 Install PX4 Dependencies

```bash
cd ~/AirMouse/PX4-Autopilot

bash ./Tools/setup/ubuntu.sh
```

The installation may take some time.

When complete:

```bash
sudo reboot
```

---

## 6.4 Verify PX4 SITL

After reboot:

```bash
cd ~/AirMouse/PX4-Autopilot
```

Run the standard PX4 Gazebo simulation:

```bash
make px4_sitl gz_x500
```

This launches:

```text
PX4 SITL
    │
    ▼
Gazebo Harmonic
    │
    ▼
Simulated X500 Drone
```

The standard PX4 command for the Gazebo simulation is:

```bash
make px4_sitl gz_x500
```

PX4 also provides variants such as depth-camera and vision-odometry models.

---

# 7. Install ROS 2 ↔ Gazebo Harmonic Integration

For ROS 2 Humble with Gazebo Harmonic, install the Harmonic-specific ROS-Gazebo interface packages:

```bash
sudo apt update

sudo apt install -y ros-humble-ros-gzharmonic
```

Source ROS 2:

```bash
source /opt/ros/humble/setup.bash
```

Verify:

```bash
ros2 pkg list | grep ros_gz
```

Expected packages include:

```text
ros_gz_bridge
ros_gz_sim
ros_gz_image
```

Do not mix incompatible Gazebo bridge packages.

For this project:

```text
ROS 2 Humble
        │
        ▼
Gazebo Harmonic
        │
        ▼
ros-humble-ros-gzharmonic
```

PX4 specifically recommends the Harmonic-compatible ROS-Gazebo integration for this configuration.

---

# 8. Install Micro XRCE-DDS Agent

PX4 uses Micro XRCE-DDS to communicate with ROS 2.

Install the agent:

```bash
sudo apt install -y ros-humble-micro-ros-agent
```

Verify:

```bash
ros2 run micro_ros_agent micro_ros_agent --help
```

The communication architecture is:

```text
PX4 SITL
    │
    │ uXRCE-DDS
    ▼
Micro XRCE-DDS Agent
    │
    ▼
ROS 2
```

For the AirMouse project:

```text
┌───────────────┐
│ PX4 SITL      │
└───────┬───────┘
        │
        ▼
┌──────────────────────┐
│ XRCE-DDS Agent       │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ ROS 2 Humble         │
│                      │
│ • PX4 topics         │
│ • Odometry           │
│ • Commands           │
│ • Telemetry          │
└──────────────────────┘
```

---

# 9. Install PX4 ROS 2 Message Packages

Create a ROS 2 workspace:

```bash
mkdir -p ~/AirMouse/ros2_ws/src
cd ~/AirMouse/ros2_ws/src
```

Clone `px4_msgs`:

```bash
git clone https://github.com/PX4/px4_msgs.git
```

Clone the PX4 ROS 2 interface examples:

```bash
git clone https://github.com/PX4/px4_ros_com.git
```

Install dependencies:

```bash
cd ~/AirMouse/ros2_ws

rosdep install --from-paths src --ignore-src -r -y
```

Build:

```bash
colcon build --symlink-install
```

Source:

```bash
source install/setup.bash
```

Add the workspace to `.bashrc`:

```bash
echo "source ~/AirMouse/ros2_ws/install/setup.bash" >> ~/.bashrc
```

Reload:

```bash
source ~/.bashrc
```

---

# 10. Install Nav2

Nav2 will be used for autonomous navigation.

The main packages are:

```text
navigation2
nav2_bringup
```

Install:

```bash
sudo apt update

sudo apt install -y \
    ros-humble-navigation2 \
    ros-humble-nav2-bringup
```

For testing with the TurtleBot3 Gazebo example:

```bash
sudo apt install -y ros-humble-turtlebot3*
```

Nav2's official installation uses the `navigation2` and `nav2-bringup` packages.

Verify:

```bash
ros2 pkg list | grep nav2
```

You should see packages such as:

```text
nav2_amcl
nav2_bt_navigator
nav2_controller
nav2_costmap_2d
nav2_lifecycle_manager
nav2_map_server
nav2_planner
nav2_waypoint_follower
```

---

# 11. Nav2 Simulation Time

Because Gazebo is the simulation clock, Nav2 must use simulation time.

For Nav2 launches:

```bash
use_sim_time:=true
```

Example:

```bash
ros2 launch nav2_bringup navigation_launch.py \
    use_sim_time:=true
```

The simulation time architecture is:

```text
Gazebo Clock
     │
     ▼
/clock
     │
     ▼
ROS 2 Nodes
     │
     ├── SLAM
     ├── Nav2
     ├── Sensors
     └── Mission Logic
```

---

# 12. Install SLAM Toolbox

For the AirMouse 2D mapping system, install SLAM Toolbox:

```bash
sudo apt install -y ros-humble-slam-toolbox
```

Verify:

```bash
ros2 pkg list | grep slam_toolbox
```

The intended architecture is:

```text
Simulated 2D LiDAR
        │
        ▼
   /scan
        │
        ▼
   SLAM Toolbox
        │
        ├── /map
        └── /tf
```

The resulting map will be sent to the Ground Station dashboard.

---

# 13. Install Camera and Image Transport Packages

Install ROS image packages:

```bash
sudo apt install -y \
    ros-humble-image-transport \
    ros-humble-image-transport-plugins \
    ros-humble-cv-bridge \
    ros-humble-camera-info-manager
```

The simulation architecture will represent your RunCam WiFiLink V2 as:

```text
Gazebo Camera
      │
      ▼
ROS 2 Image Topic
      │
      ▼
Ground Station
      │
      ├── Live Video
      ├── YOLO
      └── Survivor Detection
```

Example topic:

```text
/camera/image_raw
```

---

# 14. Install OpenCV

```bash
sudo apt install -y \
    python3-opencv \
    libopencv-dev
```

Verify:

```bash
python3 -c "import cv2; print(cv2.__version__)"
```

---

# 15. Install Python Machine Learning Environment

Create a virtual environment:

```bash
python3 -m venv ~/AirMouse/venv
```

Activate:

```bash
source ~/AirMouse/venv/bin/activate
```

Upgrade pip:

```bash
pip install --upgrade pip
```

Install basic packages:

```bash
pip install \
    numpy \
    scipy \
    matplotlib \
    opencv-python
```

For survivor detection:

```bash
pip install ultralytics
```

Verify:

```bash
python3 -c "from ultralytics import YOLO; print('YOLO OK')"
```

---

# 16. Install QGroundControl 4.4.5

This project specifically uses:

```text
QGroundControl v4.4.5
```

The official QGroundControl v4.4.5 release is available from the QGroundControl GitHub releases.

Download the Linux x86_64 AppImage from the **v4.4.5 release assets**.

Create a directory:

```bash
mkdir -p ~/Applications/QGroundControl
cd ~/Applications/QGroundControl
```

After downloading the AppImage, make it executable:

```bash
chmod +x QGroundControl*.AppImage
```

Run:

```bash
./QGroundControl*.AppImage
```

---

## 16.1 Install QGroundControl Dependencies

```bash
sudo apt install -y \
    libfuse2 \
    libxcb-xinerama0 \
    libxkbcommon-x11-0 \
    libxcb-cursor-dev \
    python3-gi \
    python3-gst-1.0
```

QGroundControl's Linux installation requires the AppImage to be executable and may require `libfuse2` and related runtime dependencies.

---

## 16.2 Serial Port Permissions

Add the current user to the `dialout` group:

```bash
sudo usermod -aG dialout "$USER"
```

Log out and log back in, or reboot:

```bash
sudo reboot
```

---

## 16.3 Disable ModemManager

ModemManager can interfere with serial ports used by robotics software.

Recommended:

```bash
sudo systemctl mask --now ModemManager.service
```

Alternatively:

```bash
sudo apt remove --purge modemmanager
```

---

# 17. Verify QGroundControl and PX4 SITL

Start PX4:

```bash
cd ~/AirMouse/PX4-Autopilot

make px4_sitl gz_x500
```

Start QGroundControl:

```bash
cd ~/Applications/QGroundControl

./QGroundControl*.AppImage
```

Expected:

```text
┌──────────────────┐
│ PX4 SITL         │
└────────┬─────────┘
         │
         │ MAVLink
         ▼
┌──────────────────┐
│ QGroundControl   │
│                  │
│ • Vehicle status │
│ • Attitude       │
│ • Battery        │
│ • Flight mode    │
│ • Telemetry      │
└──────────────────┘
```

---

# 18. AirMouse Simulation Workspace

The final project workspace should look like:

```text
~/AirMouse/
│
├── PX4-Autopilot/
│
├── ros2_ws/
│   ├── src/
│   │   │
│   │   ├── px4_msgs/
│   │   │
│   │   ├── px4_ros_com/
│   │   │
│   │   ├── airmouse_description/
│   │   │   ├── urdf/
│   │   │   ├── meshes/
│   │   │   ├── config/
│   │   │   └── launch/
│   │   │
│   │   ├── airmouse_gazebo/
│   │   │   ├── worlds/
│   │   │   ├── models/
│   │   │   └── launch/
│   │   │
│   │   ├── airmouse_companion/
│   │   │   ├── mavlink_gateway/
│   │   │   ├── sensor_gateway/
│   │   │   └── failsafe/
│   │   │
│   │   ├── airmouse_slam/
│   │   │
│   │   ├── airmouse_navigation/
│   │   │
│   │   ├── airmouse_survivor_detection/
│   │   │
│   │   ├── airmouse_localisation/
│   │   │
│   │   └── airmouse_ground_station/
│   │
│   └── install/
│
├── venv/
│
└── Applications/
    └── QGroundControl/
```

---

# 19. Installation Verification Checklist

## 19.1 Ubuntu

```bash
lsb_release -a
```

Expected:

```text
Ubuntu 22.04
```

---

## 19.2 ROS 2

```bash
echo $ROS_DISTRO
```

Expected:

```text
humble
```

Test:

```bash
ros2 run demo_nodes_cpp talker
```

---

## 19.3 colcon

```bash
colcon --version
```

If unavailable:

```bash
sudo apt install -y python3-colcon-common-extensions
```

---

## 19.4 Gazebo

```bash
gz sim --version
```

Expected:

```text
Gazebo Harmonic
```

---

## 19.5 PX4

```bash
cd ~/AirMouse/PX4-Autopilot

make px4_sitl gz_x500
```

---

## 19.6 Nav2

```bash
ros2 pkg list | grep nav2
```

---

## 19.7 SLAM Toolbox

```bash
ros2 pkg list | grep slam_toolbox
```

---

## 19.8 QGroundControl

Launch:

```bash
./QGroundControl*.AppImage
```

Confirm that it detects PX4 SITL.

---

# 20. Complete First Test

Open separate terminals.

## Terminal 1 — PX4 + Gazebo

```bash
source /opt/ros/humble/setup.bash

cd ~/AirMouse/PX4-Autopilot

make px4_sitl gz_x500
```

---

## Terminal 2 — ROS 2 Environment

```bash
source /opt/ros/humble/setup.bash

source ~/AirMouse/ros2_ws/install/setup.bash
```

Check topics:

```bash
ros2 topic list
```

---

## Terminal 3 — QGroundControl

```bash
cd ~/Applications/QGroundControl

./QGroundControl*.AppImage
```

---

## Terminal 4 — ROS 2 Topic Monitoring

```bash
source /opt/ros/humble/setup.bash

ros2 topic list
```

Check ROS 2 node graph:

```bash
ros2 node list
```

Check PX4 topics:

```bash
ros2 topic list | grep fmu
```

---

# 21. Final Installation Test

The installation is successful when the following system works:

```text
                 ┌─────────────────┐
                 │   Gazebo        │
                 │                 │
                 │ Simulated Drone │
                 └────────┬────────┘
                          │
                          ▼
                 ┌─────────────────┐
                 │   PX4 SITL      │
                 │                 │
                 │ Virtual FC      │
                 └────────┬────────┘
                          │
                          ▼
                 ┌─────────────────┐
                 │     ROS 2       │
                 │                 │
                 │  PX4 Interface  │
                 └────────┬────────┘
                          │
             ┌────────────┼────────────┐
             ▼            ▼            ▼
          SLAM          Nav2       Mission Logic
             │            │            │
             └────────────┼────────────┘
                          ▼
                 ┌─────────────────┐
                 │ Ground Station  │
                 │                 │
                 │ Map             │
                 │ Drone Position  │
                 │ Survivor Tags   │
                 │ Mission Status  │
                 └─────────────────┘

                          ▲
                          │ MAVLink
                          ▼

                 ┌─────────────────┐
                 │ QGroundControl  │
                 │     4.4.5       │
                 └─────────────────┘
```

---

# 22. Important Project Rule

Do not start developing the complete NIDAR AirMouse system immediately.

Use this development sequence:

```text
PHASE 1
───────
ROS 2
  │
  └── Verify installation

PHASE 2
───────
PX4 SITL + Gazebo
  │
  └── Make simulated drone fly

PHASE 3
───────
Simulated LiDAR
  │
  └── Verify /scan

PHASE 4
───────
SLAM Toolbox
  │
  └── Generate live 2D map

PHASE 5
───────
PX4 + ROS 2
  │
  └── Send autonomous commands

PHASE 6
───────
Nav2
  │
  └── Autonomous navigation

PHASE 7
───────
Simulated RGB Camera
  │
  └── Live video

PHASE 8
───────
YOLO
  │
  └── Survivor detection

PHASE 9
───────
Drone Pose + Camera Detection
  │
  └── Survivor coordinates

PHASE 10
────────
Coordinate → Grid Box
  │
  └── Survivor map marker

PHASE 11
────────
Mission State Machine
  │
  ├── Enter
  ├── Explore
  ├── Detect
  ├── Localise
  ├── Map
  ├── Exit
  └── Land
```

The final objective is to have the simulation behave like this:

```text
                 START
                   │
                   ▼
              TAKEOFF
                   │
                   ▼
            ENTER MAZE
                   │
                   ▼
        ┌───────────────────────┐
        │ Autonomous Exploration│
        │                       │
        │  LiDAR → SLAM         │
        │  Camera → YOLO        │
        │  Pose → Localisation  │
        │  Nav2 → Navigation    │
        └──────────┬────────────┘
                   │
                   ▼
            SURVIVOR FOUND
                   │
                   ▼
            GRID LOCATION
                   │
                   ▼
              MAP MARKER
                   │
                   ▼
          CONTINUE EXPLORING
                   │
                   ▼
              EXIT MAZE
                   │
                   ▼
                LAND
```

This gives you a simulation architecture that directly maps to your future hardware:

```text
REAL HARDWARE                         SIMULATION
──────────────────────────────────────────────────────
MicoAir Flight Controller     →       PX4 SITL
RPLIDAR C1M1 R2                →       Gazebo 2D LiDAR
MTF-01 Optical Flow            →       Simulated optical flow
RunCam WiFiLink V2             →       Gazebo RGB camera
Raspberry Pi 5                 →       Companion ROS 2 node
TP-Link AX12                   →       Local ROS 2 network
MAVLink                        →       PX4 SITL MAVLink
Ground Station PC              →       Same PC / separate process
SLAM                           →       SLAM Toolbox
Autonomous Navigation          →       Nav2
Survivor Detection             →       YOLO
Mission Display                →       Custom GCS + QGroundControl
```

---

## Official References

* [ROS 2 Humble documentation](https://docs.ros.org/en/humble/?utm_source=chatgpt.com)
* [PX4 Ubuntu development environment](https://docs.px4.io/main/en/dev_setup/dev_env_linux_ubuntu?utm_source=chatgpt.com)
* [PX4 Gazebo simulation](https://docs.px4.io/main/en/sim_gazebo_gz/index?utm_source=chatgpt.com)
* [PX4 ROS 2 User Guide](https://docs.px4.io/main/en/ros2/user_guide?utm_source=chatgpt.com)
* [Nav2 installation documentation](https://docs.nav2.org/getting_started/index.html?utm_source=chatgpt.com)
* [QGroundControl v4.4.5 releases](https://github.com/mavlink/qgroundcontrol/releases?utm_source=chatgpt.com)
