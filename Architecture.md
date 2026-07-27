                           ┌─────────────────────────────────────┐
                           │          GROUND STATION PC          │
                           │                                     │
                           │          ROS 2 HUMBLE               │
                           │                                     │
                           │  ┌───────────────────────────────┐  │
                           │  │       MISSION MANAGER         │  │
                           │  │                               │  │
                           │  │  SEARCH → DETECT → EXIT       │  │
                           │  └───────────────┬───────────────┘  │
                           │                  │                  │
                           │  ┌───────────────▼───────────────┐  │
                           │  │       AUTONOMOUS NAVIGATION   │  │
                           │  │                               │  │
                           │  │ Frontier Exploration          │  │
                           │  │ Path Planning                 │  │
                           │  │ Obstacle Avoidance            │  │
                           │  │ Exit Planning                 │  │
                           │  └───────────────┬───────────────┘  │
                           │                  │                  │
                           │  ┌───────────────▼───────────────┐  │
                           │  │             SLAM              │  │
                           │  │                               │  │
                           │  │ LiDAR + IMU + Odometry        │  │
                           │  │                               │  │
                           │  │       → 2D Occupancy Map      │  │
                           │  └───────────────┬───────────────┘  │
                           │                  │                  │
                           │  ┌───────────────▼───────────────┐  │
                           │  │       SURVIVOR LOCALISATION   │  │
                           │  │                               │  │
                           │  │ Video → Detection → Position  │  │
                           │  │                               │  │
                           │  │       → Grid Coordinate       │  │
                           │  └───────────────┬───────────────┘  │
                           │                  │                  │
                           │  ┌───────────────▼───────────────┐  │
                           │  │            GCS / UI           │  │
                           │  │                               │  │
                           │  │ Live Video                    │  │
                           │  │ Live Map                      │  │
                           │  │ Survivor Markers              │  │
                           │  │ Mission Status                │  │
                           │  └───────────────────────────────┘  │
                           │                                     │
                           └──────────────────┬──────────────────┘
                                              │
                                    TP-Link AX12
                                  LOCAL Wi-Fi ONLY
                                              │
                                              ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                             DRONE                                       │
│                                                                         │
│                         RASPBERRY PI 5                                  │
│                                                                         │
│  ┌─────────────────┐     ┌──────────────────────────────────────────┐   │
│  │ RPLIDAR C1M1    │────►│                                          │   │
│  └─────────────────┘     │       DRONE COMPANION COMPUTER           │   │
│                          │                                          │   │
│  ┌─────────────────┐     │  Sensor Data Acquisition                 │   │
│  │ MTF-01          │────►│  LiDAR Driver                            │   │
│  │ Optical Flow    │     │  MAVLink Router                          │   │
│  └─────────────────┘     │  Wi-Fi Communication                     │   │
│                          │  Command Forwarding                      │   │
│  ┌─────────────────┐     │  Heartbeat / Failsafe                    │   │
│  │ IMU from FC     │◄───►│                                          │   │
│  └─────────────────┘     └──────────────────────┬───────────────────┘   │
│                                                  │ MAVLink              │
│                                                  ▼                      │
│                                  ┌─────────────────────────┐            │
│                                  │       MICOAIR FC        │            │
│                                  │                         │            │
│                                  │  State Estimation       │            │
│                                  │  Attitude Control       │            │
│                                  │  Position Control       │            │
│                                  │  Motor Control          │            │
│                                  │  Failsafes              │            │
│                                  └─────────────────────────┘            │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘


             ┌───────────────────────────────┐
             │       RUNCAM WIFILINK V2      │
             │                               │
             │       Dedicated RF Video      │
             └───────────────┬───────────────┘
                             │
                             ▼
                   ┌──────────────────┐
                   │ RF Video Receiver│
                   └────────┬─────────┘
                            │
                            ▼
                    GROUND STATION PC
                    

                          NIDAR AirMouse SIMULATION

┌────────────────────────────────────────────────────────────────────┐
│                         GROUND STATION PC                          │
│                                                                    │
│  ┌──────────┐  ┌───────────┐  ┌────────────┐  ┌────────────────┐   │
│  │ SLAM     │  │ YOLO      │  │ NAVIGATION │  │ MISSION        │   │
│  │          │  │ SURVIVOR  │  │            │  │ MANAGER        │   │
│  └────┬─────┘  └─────┬─────┘  └──────┬─────┘  └───────┬────────┘   │
│       │              │               │                │            │ 
│       └──────────────┴───────────────┴────────────────┘            │
│                              │                                     │
│                         GCS DASHBOARD                              │
│                 Map + Video + Survivor Tags                        │
└──────────────────────────────┬─────────────────────────────────────┘
                               │
                         LOCAL NETWORK
                       Simulated AX12 Wi-Fi
                               │
┌──────────────────────────────▼─────────────────────────────────────┐
│                  SIMULATED RASPBERRY PI 5                          │
│                                                                    │
│  Gazebo LiDAR ──► Sensor Gateway                                   │
│                                                                    │
│  PX4 MAVLink ◄──► MAVLink Bridge                                   │
│                                                                    │
│  Ground Commands ──► Command Forwarder                             │
│                                                                    │
│  Heartbeat ──► Link Monitor                                        │
└──────────────────────────────┬─────────────────────────────────────┘
                               │
                             MAVLink
                               │
┌──────────────────────────────▼─────────────────────────────────────┐
│                             PX4 SITL                               │
│                                                                    │
│  EKF │ Flight Control │ Failsafe │ Motor Control                   │
└──────────────────────────────┬─────────────────────────────────────┘
                               │
                               ▼
┌────────────────────────────────────────────────────────────────────┐
│                            GAZEBO                                  │
│                                                                    │
│  Drone + Propeller Guards                                          │
│  RPLIDAR Simulation                                                │
│  Optical Flow Simulation                                           │
│  IMU Simulation                                                    │
│  RGB Camera Simulation                                             │
│  15 m × 15 m Unknown Maze                                          │
│  Corridors + Rooms + Debris + Survivors                            │
└────────────────────────────────────────────────────────────────────┘

Transmit:

* LiDAR scans
* IMU/odometry data
* Drone telemetry
* ROS 2 topics
* Map data
* Mission status
* Commands
* Navigation information
