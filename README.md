# Veddar VESC Interface

![ROS2 CI Workflow](https://github.com/f1tenth/vesc/workflows/ROS2%20CI%20Workflow/badge.svg)

Packages to interface with Veddar VESC motor controllers. See https://vesc-project.com/ for details

This is a ROS2 implementation of the ROS1 driver using the new serial driver located in [transport drivers](https://github.com/ros-drivers/transport_drivers).

## How to test

The driver requires a continuous stream of motor commands. If no finite motor
command is received for `motor_command_timeout_sec` (default 0.25 seconds), it
sends zero RPM over serial every 50 ms. Duty-cycle, current, brake, speed and
position commands refresh this timeout; servo commands and telemetry do not.
The timer uses a steady clock and remains active independently of ROS simulated
time. Motion can resume when a new motor command arrives.

This protects against stopped upstream ROS nodes while the driver and serial
link still work. A stopped driver, computer or serial connection requires the
VESC firmware's own timeout protection; the firmware configuration has not been
verified or changed by this software change.

1. Clone this repository and [transport drivers](https://github.com/ros-drivers/transport_drivers) into `src`.
2. `rosdep update && rosdep install --from-paths src -i -y`
3. Plug in the VESC with a USB cable.
4. Modify `vesc/vesc_driver/params/vesc_config.yaml` to reflect any changes.
5. Build the packages `colcon build`
6. `ros2 launch vesc_driver vesc_driver_node.launch.py`
7. If prompted "permission denied" on the serial port: `sudo chmod 777 /dev/ttyACM0`
