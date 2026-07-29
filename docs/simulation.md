Ubuntu 22.04
      │
      ▼
ROS 2 Humble
      │
      ├──────────────► Gazebo
      │                └── Indoor maze
      │                └── Simulated drone
      │                └── Simulated LiDAR
      │                └── Simulated camera
      │
      ▼
PX4 SITL
      │
      ▼
MAVLink
      │
      ▼
ROS 2 autonomy system
      │
      ├── SLAM
      ├── YOLO
      ├── Navigation
      ├── Survivor localisation
      └── GCS


To be created own model:

AirMouse
├── Drone body
├── 4 motors
├── Propeller guards
├── RPLIDAR sensor
├── Optical flow sensor
├── IMU
└── RunCam-style RGB camera

                   RGB CAMERA
                       │
                       ▼
                 ┌───────────┐
                 │           │
            ┌────┴───────────┴────┐
            │      AIR MOUSE       │
            │                      │
     LiDAR  ◄──── Raspberry Pi ───► MAVLink
            │                      │
            └──────────────────────┘
              │    │    │    │
             Motor Motor Motor Motor


Room Parameters:

15 m × 15 m

✓ 1 m corridors
✓ 2 m × 2 m rooms
✓ 8 ft ceiling
✓ Unknown maze layout
✓ Obstacles
✓ Survivor dummies
✓ Entry/exit location

