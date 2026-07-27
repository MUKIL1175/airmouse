# NIDAR AirMouse

## Autonomous GPS-Denied Indoor Search, Mapping & Survivor Localisation Drone

> An autonomous aerial rescue system designed to explore unknown indoor maze-like environments, generate a live 2D map, detect survivors, localise them on the map, and safely return through the designated exit.

---

## 1. Project Overview

**NIDAR AirMouse** is an autonomous indoor search-and-rescue drone developed for the **NIDAR Drone Innovation Challenge — Track 1: Drone Innovation**.

The system is designed for environments where:

* GPS/GNSS is unavailable
* Rescue personnel cannot safely enter immediately
* The environment contains corridors, junctions, turns, rooms and obstacles
* The internal layout is unknown before the mission
* Survivors may be located at unknown positions
* The drone must autonomously explore, map and report survivor locations

The final system will operate without manual piloting during the mission.

The operator will only:

1. Prepare the system
2. Start the mission
3. Monitor the mission through the Ground Control Station
4. Trigger emergency abort if required

All navigation, mapping, survivor detection and survivor localisation will be performed autonomously.

---

# 2. Mission Objective

The AirMouse must autonomously:

```text
                 ┌───────────────┐
                 │  DESIGNATED   │
                 │    ENTRY      │
                 └───────┬───────┘
                         │
                         ▼
                  ┌─────────────┐
                  │   TAKEOFF   │
                  └──────┬──────┘
                         │
                         ▼
              ┌──────────────────────┐
              │   UNKNOWN INDOOR     │
              │       MAZE           │
              │                      │
              │  ┌────┐   ┌─────┐    │
              │  │    │───│     │    │
              │  │    │   │  S  │    │
              │  └─┬──┘   └──┬──┘    │
              │    │          │       │
              │  ┌─┴──────────┴─┐     │
              │  │     S        │     │
              │  └──────┬───────┘     │
              │         │             │
              │      ┌──┴──┐          │
              │      │  S  │          │
              │      └─────┘          │
              └─────────┬────────────┘
                        │
                        ▼
                 ┌───────────────┐
                 │  DESIGNATED   │
                 │     EXIT      │
                 └───────────────┘
```

The system must:

* Enter the maze
* Navigate autonomously
* Avoid walls and obstacles
* Build a 2D map
* Detect survivors
* Determine survivor locations
* Assign survivors to map/grid coordinates
* Display survivor markers
* Continue exploring
* Return to the designated exit
* Complete the mission within the permitted time

---

# 3. Final System Outcome

The final outcome of the project is not simply a flying drone.

The final outcome is a complete **Autonomous Indoor Rescue Platform**.

```text
                 ┌───────────────────────────┐
                 │       AIRMOOUSE DRONE      │
                 │                           │
                 │  Sensors                  │
                 │  Flight Controller        │
                 │  Companion Computer        │
                 │  Communication             │
                 └──────────────┬────────────┘
                                │
                                │ Local Wireless Link
                                │
                                ▼
                 ┌───────────────────────────┐
                 │      GROUND STATION       │
                 │                           │
                 │  Live Map                 │
                 │  Drone Position           │
                 │  Survivor Locations       │
                 │  Live Camera              │
                 │  Mission Status           │
                 │  Navigation Status        │
                 └───────────────────────────┘
```

At the end of the mission, the Ground Control Station should show:

```text
┌───────────────────────────────────────────────┐
│              AIRMOOUSE MISSION                │
│                                               │
│  Mission: SEARCH AND RESCUE                   │
│  Status: EXPLORING                            │
│  Flight Time: 08:42                           │
│  Battery: 78%                                 │
│                                               │
│  ┌──────────────────────┐  ┌───────────────┐  │
│  │                      │  │ LIVE CAMERA   │  │
│  │       2D MAP         │  │               │  │
│  │                      │  │    CAMERA     │  │
│  │    ▲ DRONE           │  │               │  │
│  │                      │  └───────────────┘  │
│  │       ● SURVIVOR 1   │                      │
│  │                      │  Survivor Found: 3  │
│  │          ● SURVIVOR 2│  Mission: 62%       │
│  │                      │                      │
│  └──────────────────────┘                      │
│                                               │
│  [AUTONOMOUS] [GPS-DENIED] [SLAM ACTIVE]     │
└───────────────────────────────────────────────┘
```

---

# 4. System Architecture

## 4.1 Final Hardware Architecture

```text
                         ┌──────────────────────┐
                         │       DRONE          │
                         │                      │
                         │  MicoAir FC          │
                         │  PX4 / Flight Stack  │
                         │                      │
                         │  RPLIDAR C1M1 R2     │
                         │  MTF-01 Optical Flow │
                         │  RunCam WiFiLink V2  │
                         │  Raspberry Pi 5      │
                         │  ELRS / Telemetry    │
                         └──────────┬───────────┘
                                    │
                                    │ Local Communication
                                    │
                   ┌────────────────┴────────────────┐
                   │                                 │
                   ▼                                 ▼
          ┌─────────────────┐              ┌─────────────────┐
          │ TP-Link AX12    │              │ RunCam WiFiLink │
          │ Local Network   │              │ V2 RF Video     │
          └────────┬────────┘              └────────┬────────┘
                   │                                │
                   ▼                                ▼
          ┌─────────────────────────────────────────────┐
          │              GROUND STATION PC               │
          │                                             │
          │  ROS 2                                       │
          │  SLAM                                         │
          │  Navigation                                    │
          │  Survivor Detection                            │
          │  Survivor Localisation                         │
          │  Mission Dashboard                             │
          │                                             │
          │  QGroundControl 4.4.5                         │
          └─────────────────────────────────────────────┘
```

---

# 5. Hardware-to-Software Mapping

| Real Hardware                       | Simulation Equivalent                |
| ----------------------------------- | ------------------------------------ |
| MicoAir F405/H743 Flight Controller | PX4 SITL                             |
| Real Motors and ESCs                | Gazebo simulated motors              |
| RPLIDAR C1M1 R2                     | Gazebo 2D LiDAR                      |
| MTF-01 Optical Flow                 | Simulated optical flow               |
| RunCam WiFiLink V2                  | Gazebo RGB camera                    |
| Raspberry Pi 5                      | Simulated companion computer process |
| MAVLink                             | PX4 SITL MAVLink                     |
| TP-Link AX12                        | Local simulated network              |
| Ground Station PC                   | ROS 2 processing environment         |
| Real indoor arena                   | Gazebo maze world                    |

The purpose of the simulation is to ensure that the software architecture can later be transferred to the real drone.

---

# 6. Core Design Philosophy

The system follows a distributed architecture.

## Flight Controller

Responsible for:

* Stabilisation
* Motor control
* Attitude estimation
* Low-level flight safety
* Flight modes
* Emergency response

```text
                ┌──────────────────┐
                │  FLIGHT CONTROL  │
                │                  │
                │  Stabilisation   │
                │  Attitude        │
                │  Motor Output    │
                │  Failsafe        │
                └──────────────────┘
```

---

## Raspberry Pi 5 Companion Computer

The Raspberry Pi 5 acts as the onboard autonomy and communication computer.

It is responsible for:

* Receiving LiDAR data
* Receiving camera/video-related data
* Communicating with the flight controller
* Sending telemetry to the Ground Station
* Receiving autonomous commands
* Monitoring communication health
* Supervising failsafe conditions

The Pi 5 does not need to perform every heavy computation.

```text
              ┌────────────────────────────┐
              │      RASPBERRY PI 5        │
              │                            │
              │  MAVLink Communication     │
              │  Sensor Gateway            │
              │  Telemetry Gateway         │
              │  Command Gateway           │
              │  Health Monitoring         │
              │  Failsafe Supervision      │
              └──────────────┬─────────────┘
                             │
                             ▼
                       GROUND STATION
```

---

## Ground Station PC

The Ground Station PC performs computationally intensive processing.

This includes:

* SLAM
* Map generation
* Navigation planning
* Survivor detection
* Image processing
* Survivor localisation
* Grid coordinate calculation
* Mission dashboard
* Mission logging

This design matches the actual competition requirement because the Ground Station is the main mission supervision and display system.

---

# 7. Main Software Pipeline

```text
┌─────────────┐
│   LiDAR     │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ /scan       │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ SLAM        │
│ Toolbox     │
└──────┬──────┘
       │
       ├──────────────► /map
       │
       └──────────────► Drone Pose
                              │
                              ▼
                        ┌────────────┐
                        │ Nav2       │
                        └─────┬──────┘
                              │
                              ▼
                     Autonomous Navigation
```

At the same time:

```text
┌───────────────┐
│ RGB Camera    │
└───────┬───────┘
        │
        ▼
┌───────────────┐
│ YOLO Detector  │
└───────┬───────┘
        │
        ▼
┌──────────────────────┐
│ Survivor Detection   │
└──────────┬───────────┘
           │
           ▼
     Camera Position
           │
           ▼
   Drone Pose + Map
           │
           ▼
┌──────────────────────┐
│ Survivor Localisation│
└──────────┬───────────┘
           │
           ▼
    Grid Coordinate
           │
           ▼
┌──────────────────────┐
│ Map Survivor Marker  │
└──────────────────────┘
```

---

# 8. Autonomous Mission State Machine

The entire mission is controlled by a mission state machine.

```text
                    ┌───────────┐
                    │   IDLE    │
                    └─────┬─────┘
                          │
                          ▼
                    ┌───────────┐
                    │  START    │
                    └─────┬─────┘
                          │
                          ▼
                    ┌───────────┐
                    │  TAKEOFF  │
                    └─────┬─────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │  ENTER MAZE      │
                 └────────┬─────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │   EXPLORE         │◄─────────┐
                 └────────┬─────────┘          │
                          │                    │
              ┌───────────┴───────────┐        │
              │                       │        │
              ▼                       ▼        │
       ┌──────────────┐       ┌──────────────┐ │
       │ No Survivor  │       │ Survivor     │ │
       │ Detected     │       │ Detected     │ │
       └──────┬───────┘       └──────┬───────┘ │
              │                      │         │
              │                      ▼         │
              │              ┌──────────────┐  │
              │              │ Localise     │  │
              │              │ Survivor     │  │
              │              └──────┬───────┘  │
              │                     │          │
              │                     ▼          │
              │              ┌──────────────┐  │
              │              │ Assign Grid  │  │
              │              │ Coordinate   │  │
              │              └──────┬───────┘  │
              │                     │          │
              │                     ▼          │
              │              ┌──────────────┐  │
              │              │ Add Map      │  │
              │              │ Marker       │  │
              │              └──────┬───────┘  │
              │                     │          │
              └─────────────────────┴──────────┘
                          │
                          ▼
                   UNEXPLORED AREA?
                    │           │
                   YES          NO
                    │           │
                    └─────┐     ▼
                          │  ┌──────────┐
                          └─►│  EXIT    │
                             └────┬─────┘
                                  │
                                  ▼
                             ┌─────────┐
                             │ LAND    │
                             └────┬────┘
                                  │
                                  ▼
                             ┌─────────┐
                             │COMPLETE │
                             └─────────┘
```

---

# 9. Indoor Navigation

The drone must operate without GPS.

The navigation system will use:

```text
        LiDAR
          │
          ▼
      SLAM Map
          │
          ▼
   Estimated Position
          │
          ▼
     Navigation
          │
          ▼
   Autonomous Motion
```

The system must maintain an estimate of:

```text
        x = Position X
        y = Position Y
        θ = Heading
```

This creates the drone pose:

```text
                    y
                    ▲
                    │
                    │       ▲ Drone Heading
                    │      /
                    │     /
                    │    ●
                    │
                    └────────────────► x
```

The drone will not depend on:

* GPS
* GNSS
* Internet
* Mobile network
* Cloud services

---

# 10. Mapping System

The mapping system continuously creates a 2D occupancy map.

```text
        LiDAR Measurements
                 │
                 ▼
          ┌────────────┐
          │ SLAM        │
          │ Algorithm   │
          └─────┬──────┘
                │
                ▼
        ┌───────────────┐
        │ Occupancy Map │
        └───────┬───────┘
                │
                ▼
        Ground Station
```

The map contains:

* Free space
* Walls
* Obstacles
* Explored areas
* Unexplored areas
* Drone position
* Survivor locations

Example:

```text
┌─────────────────────────────┐
│ ███████████████████████████ │
│ █       │                   █ │
│ █       │       ● S1        █ │
│ █       └───────────────┐   █ │
│ █                       │   █ │
│ █   ▲ DRONE             │   █ │
│ █   │                   │   █ │
│ █   └───────────┐       │   █ │
│ █               │       │   █ │
│ █       ● S2    │       │   █ │
│ █               └───────┘   █ │
│ ███████████████████████████ │
└─────────────────────────────┘
```

---

# 11. Survivor Detection

The camera system is based on the **RunCam WiFiLink V2** in the real system.

For simulation, the equivalent is a simulated RGB camera.

```text
                RGB CAMERA
                    │
                    ▼
             Camera Image
                    │
                    ▼
             Object Detector
                    │
                    ▼
           Survivor Detected?
                │        │
               NO       YES
                │        │
                │        ▼
                │   Bounding Box
                │        │
                │        ▼
                │   Confidence
                │        │
                └────────┘
```

The detector should provide:

```text
{
    "class": "survivor",
    "confidence": 0.91,
    "bounding_box": [x1, y1, x2, y2],
    "timestamp": 123456789
}
```

The system should avoid duplicate detection of the same survivor.

Each survivor will receive a unique ID:

```text
SURVIVOR_001
SURVIVOR_002
SURVIVOR_003
SURVIVOR_004
SURVIVOR_005
SURVIVOR_006
```

---

# 12. Survivor Localisation

Detection alone is not enough.

The system must determine where the survivor is located in the map.

The localisation pipeline is:

```text
Camera Detection
       │
       ▼
Bounding Box
       │
       ▼
Camera Geometry
       │
       ▼
Drone Pose
       │
       ▼
SLAM Map Coordinate
       │
       ▼
Survivor Map Position
       │
       ▼
Grid Coordinate
```

Mathematically:

```text
Camera Frame
      │
      ▼
Drone Frame
      │
      ▼
Map Frame
      │
      ▼
Grid Coordinate
```

Example:

```text
Map Position:

x = 6.2 m
y = 3.7 m

Grid:

Column = 3
Row = 2

Final:

SURVIVOR_002
GRID: C3-R2
```

---

# 13. Grid Localisation

The competition arena uses a modular grid structure.

The AirMouse will convert map coordinates into grid locations.

Example:

```text
             COLUMN
        1       2       3       4
      ┌───────┬───────┬───────┬───────┐
  A   │       │       │       │       │
      ├───────┼───────┼───────┼───────┤
  B   │       │       │   S1  │       │
      ├───────┼───────┼───────┼───────┤
  C   │       │  S2   │       │       │
      ├───────┼───────┼───────┼───────┤
  D   │       │       │       │  S3   │
      └───────┴───────┴───────┴───────┘
```

The Ground Station will display:

```text
SURVIVOR 1
Location: B3

SURVIVOR 2
Location: C2

SURVIVOR 3
Location: D4
```

---

# 14. Communication Architecture

The system operates using a local communication network.

```text
                 NO INTERNET
                     │
                     ▼
              ┌──────────────┐
              │ TP-Link AX12 │
              │ Local Router │
              └──────┬───────┘
                     │
          ┌──────────┴──────────┐
          │                     │
          ▼                     ▼
   Raspberry Pi 5         Ground Station PC
   Companion Computer
```

The local network carries:

* MAVLink telemetry
* ROS 2 data
* LiDAR data
* Drone pose
* Mission status
* Navigation commands
* Survivor detection results
* Map updates

The network is isolated from:

* Internet
* GSM
* LTE
* 5G
* Cloud services
* Public Wi-Fi

The communication model is:

```text
Drone Sensors
     │
     ▼
Raspberry Pi 5
     │
     ├── MAVLink
     ├── ROS 2
     └── Local Network
             │
             ▼
       Ground Station
```

---

# 15. Video Architecture

The RunCam WiFiLink V2 provides the real-world RF video link.

The video architecture is:

```text
                REAL DRONE
                    │
                    ▼
            RunCam WiFiLink V2
                    │
                    ▼
              RF Video Link
                    │
                    ▼
             Ground Receiver
                    │
                    ▼
             Ground Station
```

In simulation:

```text
              Gazebo Camera
                    │
                    ▼
             ROS 2 Image Topic
                    │
                    ▼
             Video Processing
                    │
                    ▼
             Ground Station
```

The video feed is used for:

* Live operator monitoring
* Survivor detection
* Visual mission confirmation

The operator must only view the mission through the authorised Ground Control Station.

---

# 16. Ground Control Station

The Ground Control Station is the main human interface.

It must display:

```text
┌───────────────────────────────────────────┐
│             AIRMOOUSE GCS                 │
├───────────────────────┬───────────────────┤
│                       │                   │
│                       │   LIVE VIDEO      │
│                       │                   │
│       LIVE 2D MAP     │                   │
│                       │                   │
│       ▲ DRONE         │                   │
│                       │                   │
│       ● SURVIVOR      │                   │
│                       │                   │
├───────────────────────┴───────────────────┤
│ Mission Status: EXPLORING                 │
│ Flight Time: 08:32                        │
│ Battery: 82%                              │
│ Survivors: 3 / 6                          │
│ SLAM: ACTIVE                               │
│ Navigation: AUTONOMOUS                    │
│ Communication: CONNECTED                  │
└───────────────────────────────────────────┘
```

The Ground Station must provide:

* Live map
* Drone position
* Survivor markers
* Grid locations
* Live video
* Mission status
* Battery state
* Flight time
* Communication state
* Navigation state
* Mission completion state

---

# 17. QGroundControl Integration

QGroundControl 4.4.5 is used as the flight-control and MAVLink Ground Control Station.

It provides:

* PX4 vehicle status
* Flight mode
* Battery status
* Telemetry
* Mission status
* Vehicle position estimate
* Safety controls

The AirMouse mission dashboard may operate alongside QGroundControl.

```text
                      MAVLink
                         │
                         ▼
                 ┌───────────────┐
                 │ QGroundControl│
                 │                │
                 │ Flight Status  │
                 │ Battery        │
                 │ Safety         │
                 └───────────────┘

                         │

                         ▼

                 ┌───────────────┐
                 │ AirMouse GCS  │
                 │                │
                 │ Live Map       │
                 │ Survivor Tags  │
                 │ Video          │
                 │ Mission Logic  │
                 └───────────────┘
```

---

# 18. Software Components

The final software stack is:

```text
┌─────────────────────────────────────────────┐
│              AIRMOOUSE SOFTWARE              │
├─────────────────────────────────────────────┤
│                                             │
│  Mission State Machine                      │
│  ├── Start                                  │
│  ├── Takeoff                                │
│  ├── Explore                                │
│  ├── Detect                                 │
│  ├── Localise                               │
│  ├── Exit                                   │
│  └── Land                                   │
│                                             │
│  Navigation                                 │
│  ├── SLAM Toolbox                           │
│  ├── Nav2                                   │
│  └── Local Planner                          │
│                                             │
│  Perception                                 │
│  ├── Camera                                │
│  ├── YOLO                                  │
│  └── Survivor Detector                     │
│                                             │
│  Localisation                               │
│  ├── Drone Pose                             │
│  ├── Map Coordinate                         │
│  └── Grid Coordinate                        │
│                                             │
│  Communication                              │
│  ├── MAVLink                               │
│  ├── ROS 2                                 │
│  └── Local Network                          │
│                                             │
│  Ground Station                             │
│  ├── Map                                   │
│  ├── Video                                 │
│  ├── Survivor Markers                      │
│  └── Mission Status                        │
│                                             │
└─────────────────────────────────────────────┘
```

---

# 19. ROS 2 Package Structure

The final ROS 2 workspace will be organised as:

```text
ros2_ws/
└── src/
    │
    ├── airmouse_description/
    │   ├── urdf/
    │   ├── meshes/
    │   ├── config/
    │   └── launch/
    │
    ├── airmouse_gazebo/
    │   ├── worlds/
    │   ├── models/
    │   ├── plugins/
    │   └── launch/
    │
    ├── airmouse_sensors/
    │   ├── lidar_gateway/
    │   ├── camera_gateway/
    │   └── optical_flow/
    │
    ├── airmouse_companion/
    │   ├── mavlink_gateway/
    │   ├── telemetry/
    │   ├── command_gateway/
    │   └── failsafe/
    │
    ├── airmouse_slam/
    │   ├── slam_config/
    │   └── launch/
    │
    ├── airmouse_navigation/
    │   ├── nav2_config/
    │   ├── planners/
    │   ├── controllers/
    │   └── launch/
    │
    ├── airmouse_perception/
    │   ├── survivor_detector/
    │   ├── object_tracker/
    │   └── confidence_filter/
    │
    ├── airmouse_localisation/
    │   ├── coordinate_transform/
    │   ├── map_localisation/
    │   └── grid_localisation/
    │
    ├── airmouse_mission/
    │   ├── mission_state_machine/
    │   ├── exploration/
    │   ├── exit_planner/
    │   └── mission_manager/
    │
    └── airmouse_ground_station/
        ├── map_display/
        ├── video_display/
        ├── survivor_display/
        └── mission_dashboard/
```

---

# 20. Mission Data Flow

The complete data flow is:

```text
                  ┌──────────────┐
                  │   RPLIDAR    │
                  └──────┬───────┘
                         │
                         ▼
                    /scan
                         │
                         ▼
                    SLAM
                         │
             ┌───────────┴───────────┐
             ▼                       ▼
          /map                  Drone Pose
             │                       │
             └───────────┬───────────┘
                         │
                         ▼
                  Navigation System
                         │
                         ▼
                   Motion Command
                         │
                         ▼
                    PX4 Flight
                         │
                         ▼
                       DRONE
```

At the same time:

```text
                  ┌──────────────┐
                  │ RGB CAMERA   │
                  └──────┬───────┘
                         │
                         ▼
                       YOLO
                         │
                         ▼
                  Survivor Detection
                         │
                         ▼
                 Detection Tracking
                         │
                         ▼
                   Drone Pose
                         │
                         ▼
                    Map Coordinate
                         │
                         ▼
                    Grid Coordinate
                         │
                         ▼
                    Survivor Marker
```

All outputs converge at the Ground Station:

```text
                  ┌──────────────┐
                  │     MAP      │
                  └──────┬───────┘
                         │
                         ├──────► Drone Position
                         │
                         ├──────► Survivor Markers
                         │
                         ├──────► Grid Coordinates
                         │
                         ├──────► Live Video
                         │
                         └──────► Mission Status
```

---

# 21. Failsafe Architecture

The system must remain safe even if something goes wrong.

```text
                 ┌──────────────────────┐
                 │   SAFETY MONITOR     │
                 └──────────┬───────────┘
                            │
        ┌───────────────────┼───────────────────┐
        │                   │                   │
        ▼                   ▼                   ▼
   Low Battery       Link Lost          Mission Abort
        │                   │                   │
        └───────────────────┼───────────────────┘
                            │
                            ▼
                   ┌────────────────┐
                   │ FAILSAFE ACTION │
                   └────────┬───────┘
                            │
              ┌─────────────┴─────────────┐
              ▼                           ▼
           LAND                         ABORT
```

Required safety conditions:

* Low battery
* Communication loss
* Navigation failure
* Position estimation failure
* Obstacle proximity
* Geofence breach
* Emergency abort
* Mission timeout

---

# 22. Simulation Development Strategy

The project will be developed progressively.

## Phase 1 — Basic Simulation

```text
PX4 SITL
    │
    ▼
Gazebo
    │
    ▼
Simulated Drone
```

Goal:

* Takeoff
* Hover
* Land

---

## Phase 2 — Simulated Sensors

Add:

```text
LiDAR
Camera
Optical Flow
```

Verify:

```text
/scan
/camera/image_raw
/optical_flow
```

---

## Phase 3 — Indoor Maze

Create:

* Corridors
* Junctions
* Rooms
* Obstacles
* Entry point
* Exit point
* Survivor models

---

## Phase 4 — SLAM

```text
LiDAR
  │
  ▼
SLAM Toolbox
  │
  ▼
Live 2D Map
```

---

## Phase 5 — Autonomous Navigation

```text
Map
 │
 ▼
Nav2
 │
 ▼
Planner
 │
 ▼
Controller
 │
 ▼
PX4
```

---

## Phase 6 — Survivor Detection

```text
Camera
  │
  ▼
YOLO
  │
  ▼
Survivor Detection
```

---

## Phase 7 — Survivor Localisation

```text
Detection
    │
    ▼
Drone Pose
    │
    ▼
Map Coordinate
    │
    ▼
Grid Coordinate
```

---

## Phase 8 — Complete Mission

```text
START
  │
  ▼
TAKEOFF
  │
  ▼
EXPLORE
  │
  ├── MAP
  ├── DETECT
  ├── LOCALISE
  └── TAG
  │
  ▼
EXIT
  │
  ▼
LAND
  │
  ▼
MISSION COMPLETE
```

---

# 23. Final Simulation Success Criteria

The simulation will be considered successful when:

* [ ] PX4 SITL starts correctly
* [ ] Gazebo launches the indoor world
* [ ] Drone takes off autonomously
* [ ] GPS is not required
* [ ] LiDAR data is available
* [ ] SLAM generates a live map
* [ ] Drone pose is estimated
* [ ] Drone navigates autonomously
* [ ] Drone avoids walls and obstacles
* [ ] Camera feed is available
* [ ] Survivors are detected automatically
* [ ] Duplicate survivors are rejected
* [ ] Survivor positions are calculated
* [ ] Survivor grid coordinates are generated
* [ ] Survivor markers appear on the live map
* [ ] Mission progress is displayed
* [ ] Drone explores unknown areas
* [ ] Drone returns to the exit
* [ ] Drone lands safely
* [ ] Mission completes without operator navigation

---

# 24. Final Competition Outcome

The final AirMouse system should demonstrate the following:

```text
             UNKNOWN INDOOR ENVIRONMENT
                         │
                         ▼
                  ┌─────────────┐
                  │   AIRMOOUSE │
                  │    ENTERS   │
                  └──────┬──────┘
                         │
                         ▼
                 ┌─────────────────┐
                 │ AUTONOMOUS      │
                 │ EXPLORATION     │
                 └────────┬────────┘
                          │
              ┌───────────┼───────────┐
              ▼           ▼           ▼
           LiDAR        Camera       PX4
              │           │           │
              ▼           ▼           ▼
            SLAM        YOLO       Control
              │           │           │
              └──────┬────┴──────────┘
                     │
                     ▼
             ┌─────────────────┐
             │  LIVE 2D MAP    │
             │                 │
             │  DRONE          │
             │  SURVIVORS      │
             │  GRID LOCATIONS │
             └────────┬────────┘
                      │
                      ▼
             ┌─────────────────┐
             │  GROUND STATION │
             └────────┬────────┘
                      │
                      ▼
                MISSION COMPLETE
```

The final deliverable is therefore:

> **A fully autonomous, GPS-denied, indoor aerial search-and-rescue system that explores an unknown maze, builds a real-time 2D map, detects survivors, determines their grid locations, displays all information on a Ground Control Station, and safely exits the environment without manual navigation.**

---

# 25. Project Vision

NIDAR AirMouse is designed as a bridge between:

```text
                  HARDWARE
                     │
                     ▼
                  SIMULATION
                     │
                     ▼
                  AUTONOMY
                     │
                     ▼
                  PERCEPTION
                     │
                     ▼
                  LOCALISATION
                     │
                     ▼
                  MAPPING
                     │
                     ▼
                  NAVIGATION
                     │
                     ▼
                  DECISION MAKING
                     │
                     ▼
             REAL-WORLD RESCUE
```

The simulation will first prove the complete autonomy architecture.

The same software concepts will then be transferred to:

```text
PX4 SITL
   │
   ▼
REAL MICOAIR FLIGHT CONTROLLER

GAZEBO LiDAR
   │
   ▼
REAL RPLIDAR C1M1 R2

GAZEBO CAMERA
   │
   ▼
REAL RUNCAM WIFILINK V2

SIMULATED COMPANION COMPUTER
   │
   ▼
REAL RASPBERRY PI 5

SIMULATED LOCAL NETWORK
   │
   ▼
REAL TP-LINK AX12 NETWORK
```

**Simulation first. Hardware second. Autonomous mission finally.**

---

## Project Status

**Current Stage:** Simulation Environment Setup

### Current Objectives

* [ ] Install Ubuntu 22.04 environment
* [ ] Install ROS 2 Humble
* [ ] Install PX4 SITL
* [ ] Install Gazebo
* [ ] Install Nav2
* [ ] Install QGroundControl 4.4.5
* [ ] Create AirMouse ROS 2 workspace
* [ ] Create simulated drone
* [ ] Create indoor maze world
* [ ] Add LiDAR
* [ ] Add camera
* [ ] Implement SLAM
* [ ] Implement autonomous navigation
* [ ] Implement survivor detection
* [ ] Implement survivor localisation
* [ ] Implement grid tagging
* [ ] Implement Ground Station
* [ ] Complete autonomous mission simulation
* [ ] Transfer architecture to real hardware

---

# NIDAR AirMouse

### Autonomous Indoor Search. Map. Localise. Rescue.
