# SLAM Mobile Robot ("Slambot")

*A ROS 2 differential-drive robot that maps and localizes with LiDAR SLAM.*

## Overview

A custom differential-drive mobile robot for simultaneous localization and mapping (SLAM). It runs in both Gazebo simulation and on real hardware, using a 2D LiDAR with Cartographer / slam_toolbox for mapping and Nav2 for navigation. Includes the URDF/description, RViz configs, generated maps and the CAD for the printed chassis.

This was a mobile-robotics coursework project. I used the robot in **Gazebo simulation** (ROS 2 Jazzy, Ubuntu 24.04) to generate maps of an indoor world with two methods, slam_toolbox (with EKF-fused odometry) and Google Cartographer, and to run Nav2 on the resulting map. I then wrote a report comparing the two methods.

**Tech & tools:** ROS 2 Jazzy, Nav2, Cartographer / slam_toolbox, robot_localization (EKF), LiDAR, Gazebo Sim, RViz, URDF/xacro, ros2_control, Python, C++.

## Credits and what is mine

The ROS 2 packages, firmware, CAD and setup guides under `src_hardware/` come from **[benmay100/slambot](https://github.com/benmay100/slambot)** by Ben May, released under the Apache License 2.0 (see `src_hardware/slambot/LICENSE`). I used them as the robot platform for this project.

My own contributions are:

- `docs/Slambot_Report.docx` - the project report (robot specs, sensing and EKF fusion, kinematics, SLAM vs. Cartographer comparison, Nav2 setup).
- `ros_config/indoor_map_slam.pgm/.yaml` - the map I generated with slam_toolbox.
- `ros_config/indoor_map_cartographer.png` - an export of the Cartographer map.
- `ros_config/rviz_screenshot_*.png` and `ros_config/slambot.png` - RViz captures from my simulation runs, and a render of the robot.
- `ros_config/` - the set of configs I used for the runs (RViz layouts, Nav2 parameters, Gazebo world).

## Robot platform

| | |
|---|---|
| Drive | 4-wheel differential drive (skid-steer), `diff_drive_controller` via ros2_control |
| Motors | JGA25-371 DC gear motors with encoders, Cytron MDD10A drivers |
| Sensors | 360° 2D LiDAR (LD06 / Okdo), BNO055 IMU, Raspberry Pi Camera 3 |
| Compute | Raspberry Pi 5 (ROS 2 Jazzy) + Raspberry Pi Pico (micro-ROS firmware) |
| Localization | `robot_localization` EKF fusing wheel odometry and IMU |
| Mapping | slam_toolbox or Cartographer |
| Navigation | Nav2 (AMCL, NavFn planner, MPPI controller, collision monitor) |

The values in the URDF and controller config are: wheel radius 0.034 m, wheel separation 0.198 m, max linear speed 0.2 m/s (`slambot_bringup/config/slambot_controllers.yaml`).

## What's inside

```
docs/
└── Slambot_Report.docx          Project report
ros_config/                      Configs, maps and screenshots from my runs
├── indoor_map_slam.{pgm,yaml}       slam_toolbox map
├── indoor_map_cartographer.{pgm,yaml,png}  Cartographer map
├── indoor_world_with_qr_codes.sdf   Gazebo world used
├── nav2_sim_params.yaml             Nav2 parameters
└── gazebo_rviz_*_config.rviz        RViz layouts for each SLAM method
src_hardware/slambot/            Full workspace: ROS 2 packages + Pico firmware + Pi setup + CAD + guides
├── ros_packages/                    slambot_bringup, _description, _gazebo, _hardware,
│                                    _localization, _nav2, _slam, _scripts
├── firmware/pico_code/              micro-ROS C firmware for motors, encoders and IMU
├── scripts/raspberry_pi_setup/      Boot-mode selector and systemd service
├── hardware/cad/                    STL files for the printed chassis
└── docs/                            Setup guides (Pi camera, micro-ROS, motor-driver pinout)
```

## How to run (simulation)

Requirements: Ubuntu 24.04, ROS 2 Jazzy, Gazebo Sim (Harmonic), plus `rosdep` to install the dependencies (Nav2, slam_toolbox, cartographer_ros, robot_localization, ros2_control, ros_gz).

```bash
mkdir -p ~/slambot_ws/src
cp -r src_hardware/slambot ~/slambot_ws/src/   # firmware/, hardware/, scripts/ and docs/ carry COLCON_IGNORE
cd ~/slambot_ws
rosdep install --from-paths src --ignore-src -r -y
colcon build
source install/setup.bash
```

Drive the robot around:

```bash
ros2 launch slambot_bringup sim_teleop_only.launch.py using_joy:=false
# in another terminal
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

Build a map (`slam_type:=slamtoolbox` is the default; use `cartographer` for the other method):

```bash
ros2 launch slambot_bringup sim_map_mode.launch.py slam_type:=cartographer using_joy:=false
# when the map looks complete, in another terminal
ros2 launch slambot_slam map_saver.launch.py map_name:=my_map
```

Navigate on a saved map (by default it loads `slambot_slam/maps/indoor_map_cartographer.yaml`). Set goals with "2D Goal Pose" in RViz:

```bash
ros2 launch slambot_bringup sim_nav_mode.launch.py using_joy:=false
```

`src_hardware/slambot/ros_packages/slambot_bringup/launch/LAUNCH_FILES_GUIDE.md` lists every launch file and its arguments, including the `real_*` modes for the physical robot.

### Real hardware

Use `src_hardware/` and follow the Raspberry Pi 5 setup in `src_hardware/slambot/README.md` (LD06 LiDAR driver, micro-ROS agent, boot service). The Pico firmware in `firmware/pico_code/` **can't be built from this repo alone**: its `CMakeLists.txt` also needs `pico_uart_transport.c`, the Bosch `bno055.c/.h` driver and the prebuilt `libmicroros`, which come from the Raspberry Pi Pico SDK / micro_ros_raspberrypi_pico_sdk setup (see `docs/SetUp_MicroROS_Pico.pdf`).

## Results

The report (`docs/Slambot_Report.docx`) compares the two maps qualitatively. The map and screenshot files listed above are the outputs from my simulation runs. No quantitative accuracy metrics were recorded.

## Status & notes

- The project was run in simulation. The real-robot launch files and firmware come from the upstream project and were not part of my runs.
- Vendor datasheets and manuals (Cytron MDD10A, Okdo LiDAR, JGA25-371 product page) are not included. Get them from the manufacturers.
- `real_nav_mode.launch.py` defaults to the simulated indoor map as a placeholder. On the real robot, pass `map:=/path/to/your_map.yaml`.
- Verify topic names and frame IDs against your ROS 2 distro.

---

*By Salma Farid — Mechatronics & Robotics Engineering, BUE / LSBU. This is coursework/graduation work shared as a portfolio; I'll keep refining it.*
