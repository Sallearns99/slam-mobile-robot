# Changelog

## v1.1.0 — 2026-10-28

### Fixed
- `ros_config/gazebo_rviz_cartographer_config.rviz`: the file was empty (0 bytes). Restored it from the working copy in `slambot_slam/rviz/`.
- `slambot_nav2/launch/nav2.launch.py` (in both `src_hardware/` and `src_minimal/`): the default `map` argument pointed to `slambot_nav2/maps/indoor_map_cartographer.yaml`, but `slambot_nav2` has no `maps/` folder. It now points to `slambot_slam/maps/`, where the map lives. This only mattered when `nav2.launch.py` was run on its own; the bringup launch files already pass a valid map.
- `slambot_nav2/package.xml` (both copies): added `<exec_depend>slambot_slam</exec_depend>` to match the change above.
- `README.md`: corrected the claim that the third-party datasheets/manuals had been removed. Several are still in `src_hardware/slambot/docs/`.

### Added
- `README.md`: credits for the upstream [benmay100/slambot](https://github.com/benmay100/slambot) project (Apache-2.0) and a list of the files that are my own work, a table of the robot platform, an annotated repo layout, build and launch commands for simulation, a note on the files the firmware build needs that aren't in this repo, and the actual scope of the results.
- `CHANGELOG.md` (this file).

### Checked (no change needed)
- All Python files pass `py_compile`. `flake8 --select=F,E9` found no syntax errors or undefined names, only unused imports and variables.
- All YAML, JSON, `.rviz`, `package.xml`, SDF and COLLADA files parse.
- `slambot.urdf.xacro` expands with `xacro` for both `using_sim:=true` and `using_sim:=false`. The result is a valid tree of 10 links rooted at `base_footprint`, and every referenced mesh exists.
- Every file path built in the launch files resolves within the repo, apart from the `nav2.launch.py` default fixed above and external packages (`ldlidar_ros2`, `ros_gz_sim`).
- `gazebo/gazebo_plugins.xacro` is not valid XML (its comment block comes before the `<?xml?>` declaration), but nothing includes it; the file itself says it was replaced by `ros2_control_sim.xacro`. Left unchanged.
- Not run here: the ROS 2 launch files, Gazebo, `colcon build` of the C++ packages, and the Pico firmware build (no ROS 2 or Pico SDK is installed in this environment).

## v1.0.0

- Initial upload: ROS 2 workspace (from benmay100/slambot), simulation configs and maps, and the project report.
