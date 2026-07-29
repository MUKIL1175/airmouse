# AirMouse Simulation Model

## Goal

Create a custom Gazebo Harmonic simulation model named **gz_airmouse** for the NIDAR AirMouse project without modifying the default PX4 simulation models.

Instead of editing `gz_x500`, AirMouse will be developed as an independent Gazebo model package and then loaded by PX4 SITL.

---

# Project Structure

```text
AirMouse/
│
├── PX4-Autopilot/
│
├── ros2_ws/
│
├── gz_airmouse/
│   ├── models/
│   │   └── airmouse/
│   │       ├── model.config
│   │       ├── model.sdf
│   │       ├── meshes/
│   │       ├── materials/
│   │       └── plugins/
│   │
│   ├── worlds/
│   │   ├── airmouse_maze.sdf
│   │   ├── empty.sdf
│   │   └── test_room.sdf
│   │
│   ├── media/
│   │
│   ├── launch/
│   │
│   └── README.md
│
└── docs/
```

The PX4 repository remains completely untouched except for a custom airframe configuration.

---

# Why use a separate package?

Instead of

```
PX4
 └── Tools/simulation/gz/models/x500
```

we create

```
AirMouse/gz_airmouse/
```

Advantages:

- Keeps PX4 clean
- Easy to update PX4
- Easy GitHub repository management
- Easy sharing
- Can be used by multiple PX4 versions
- Easy to add custom worlds
- Easy to add custom plugins
- No merge conflicts after PX4 updates

---

# Step 1 — Create the Gazebo package

```
cd ~/AirMouse

mkdir gz_airmouse

cd gz_airmouse

mkdir models worlds media launch
```

Create

```
models/
    airmouse/
```

---

# Step 2 — Copy the X500 model

Instead of editing PX4 files directly, copy the model into your package.

```
cp -r \
~/AirMouse/PX4-Autopilot/Tools/simulation/gz/models/x500 \
~/AirMouse/gz_airmouse/models/airmouse
```

Now your model is independent.

---

# Step 3 — Rename everything

Rename

```
x500
```

to

```
airmouse
```

Update

```
model.config
```

```xml
<?xml version="1.0"?>

<model>

    <name>airmouse</name>

    <version>1.0</version>

    <sdf version="1.8">model.sdf</sdf>

    <author>
        <name>Monamukil</name>
    </author>

    <description>
        NIDAR AirMouse Indoor Search Drone
    </description>

</model>
```

---

# Step 4 — Edit model.sdf

The original X500 contains

- Frame
- Motors
- GPS
- Camera
- IMU
- Magnetometer

Replace it with the AirMouse hardware.

```
AirMouse

Frame
│
├── 4 Motors
├── IMU
├── Barometer
├── Magnetometer
├── Optical Flow
├── 2D LiDAR
├── RGB Camera
├── WiFiLink Camera
├── MAVLink Interface
└── Battery
```

---

# Step 5 — Add the LiDAR

Use Gazebo's GPU LiDAR.

```xml
<sensor name="lidar" type="gpu_lidar">

    <update_rate>15</update_rate>

    <ray>

        <scan>

            <horizontal>

                <samples>720</samples>

                <min_angle>-3.14159</min_angle>

                <max_angle>3.14159</max_angle>

            </horizontal>

        </scan>

        <range>

            <min>0.10</min>

            <max>12.0</max>

        </range>

    </ray>

</sensor>
```

Simulates

```
RPLIDAR C1M1 R2
```

---

# Step 6 — Optical Flow

Add the PX4 Optical Flow plugin.

```xml
<sensor name="flow" type="camera">

...

</sensor>
```

Later this maps directly to

```
MTF-01
```

---

# Step 7 — RGB Camera

```xml
<sensor name="camera" type="camera">
```

Resolution

```
640×480
```

or

```
1280×720
```

This simulates the video stream that will later come from the **RunCam WiFiLink V2**.

---

# Step 8 — MAVLink Interface

Keep the existing MAVLink interface from PX4.

```
PX4 SITL
        │
        ▼
MAVLink
        │
        ▼
QGroundControl
        │
        ▼
ROS 2
```

---

# Step 9 — Create the Maze World

```
worlds/

airmouse_maze.sdf
```

Eventually this world will contain

- Corridors
- Rooms
- Junctions
- Obstacles
- Survivor dummies
- Entry point
- Exit point

Maximum arena

```
15 × 15 m
```

---

# Step 10 — Tell Gazebo where the model lives

Instead of copying the model into PX4 every time, add your package to Gazebo's resource path.

Example:

```
export GZ_SIM_RESOURCE_PATH=$HOME/AirMouse/gz_airmouse:$GZ_SIM_RESOURCE_PATH
```

Add this line to

```
~/.bashrc
```

Reload

```
source ~/.bashrc
```

Now Gazebo can find

```
airmouse
```

without placing it inside PX4.

---

# Step 11 — Create a PX4 Airframe

Inside PX4

```
ROMFS/
    px4fmu_common/
        init.d-posix/
            airframes/
```

Copy the X500 airframe.

Example

```
4001_airmouse
```

Configure

- Frame
- Motors
- Sensors
- Parameters
- MAVLink

This file only tells PX4 how to configure the simulated vehicle.

It does **NOT** contain the Gazebo model.

---

# Step 12 — Launch AirMouse

Instead of

```
make px4_sitl gz_x500
```

you will eventually launch

```
make px4_sitl gz_airmouse
```

The launch sequence should

- Start PX4 SITL
- Load the AirMouse airframe
- Spawn the AirMouse model from `gz_airmouse`
- Load `airmouse_maze.sdf`
- Connect to QGroundControl
- Start ROS 2 bridge

---

# Final Simulation Architecture

```
                 Gazebo Harmonic
                       │
      ┌────────────────┼────────────────┐
      │                │                │
      ▼                ▼                ▼
 AirMouse Model     Maze World     Survivor Dummies
      │
      ├──────────────┐
      │              │
      ▼              ▼
  RGB Camera      2D LiDAR
      │              │
      └──────┬───────┘
             ▼
        ROS 2 Humble
             │
     ┌───────┼────────┐
     │       │        │
     ▼       ▼        ▼
 Cartographer  YOLO  Nav2
     │         │      │
     └─────┬───┴──────┘
           ▼
   Mission Manager
           │
           ▼
     MAVROS / MAVSDK
           │
           ▼
        PX4 SITL
           │
           ▼
    QGroundControl
```

---

# Future Hardware Mapping

| Simulation | Real Hardware |
|------------|---------------|
| PX4 SITL | MicoAir H743/F405 |
| GPU LiDAR | RPLIDAR C1M1 R2 |
| Optical Flow Plugin | MTF-01 |
| RGB Camera | RunCam WiFiLink V2 |
| Gazebo Physics | X500 Frame |
| MAVLink | UART Telemetry |
| ROS 2 Nodes | Raspberry Pi 5 |
| Mission Manager | Ground Station PC |
| Gazebo World | NIDAR Indoor Arena |

---

# Long-Term Goal

The objective is to ensure that every software component developed in simulation can be deployed to the real AirMouse platform with minimal changes.

Only the hardware interfaces should change:

- PX4 SITL → MicoAir Flight Controller
- Simulated LiDAR → RPLIDAR C1M1 R2
- Simulated Camera → RunCam WiFiLink V2
- Simulated Optical Flow → MTF-01
- Simulated MAVLink → Real UART MAVLink

All ROS 2 packages, autonomy logic, SLAM, Nav2, survivor detection, mission management, and Ground Control Station software should remain the same.