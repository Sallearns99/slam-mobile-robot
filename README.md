# SLAM Mobile Robot ("Slambot")

*A ROS 2 differential-drive robot that maps and localizes with LiDAR SLAM.*

## Overview

A custom differential-drive mobile robot for simultaneous localization and mapping (SLAM). It runs in both Gazebo simulation and on real hardware, using a 2D LiDAR with Cartographer / slam_toolbox for mapping and Nav2 for navigation. Includes the URDF/description, RViz configs, generated maps and the CAD for the printed chassis.

**Tech & tools:** ROS 2, Nav2, Cartographer / slam_toolbox, LiDAR, Gazebo, RViz, URDF, Python, C++.

## What's inside

- `src_minimal/` - minimal ROS 2 package set to bring up the robot
- `src_hardware/` - hardware bringup (firmware, drivers, scripts)
- `ros_config/` - RViz configs, Cartographer/slam_toolbox configs, Nav2 params, saved maps (`.pgm`/`.yaml`), Gazebo world, and the `slambot_description` package (URDF + STL meshes)
- `docs/Slambot_Report.docx` - project report

## How to use

1. Build the workspace: copy `src_minimal` (or `src_hardware`) into a ROS 2 workspace and `colcon build`.
2. Launch the description + Gazebo, then start SLAM (Cartographer or slam_toolbox) using the configs in `ros_config/`.
3. Save a map, then run Nav2 with `nav2_sim_params.yaml` for autonomous navigation.
4. For hardware, flash the firmware in `src_hardware` and wire the motor driver + LiDAR (see report).

## Status & notes

Third-party datasheets/manuals (motor driver, LiDAR) were removed for copyright; see the report for part references. Verify topic names and frame IDs against your ROS 2 distro.

---

*By Salma Farid — Mechatronics & Robotics Engineering, BUE / LSBU. This is coursework/graduation work shared as a portfolio; I'll keep refining it.*
