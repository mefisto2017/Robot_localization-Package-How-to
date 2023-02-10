# Robot_localization-Package-How-to
Guidelines on how to use the robot_localization ROS package

Three main nodes needs to be setup
- Navsat Node
- Map to Odom Node
- Odom to Base_link Node

## Navsat Node
The navsat_transform node transform the GPS coordinates (lat & long) to map coordinates (X & Y), i.e., the **sensor_msgs/NavSatFix** Message becomes a **nav_msgs/Odometry** Message.

```
<node pkg="robot_localization" type="navsat_transform_node" name="navsat_transform" clear_params="true" output="screen" >
      <remap from="odometry/filtered" to="<YOUR ODOM TOPIC>"/>
      <remap from="gps/fix" to="<YOUR GPS TOPIC>"/>
      <remap from="imu/data" to="<YOUR IMU TOPIC>"/>
</node>
```
Options:
```
  frequency: 30
  delay: 3.0
  magnetic_declination_radians: 0.00  
  yaw_offset: 0.0  
  zero_altitude: true
  broadcast_utm_transform: true
  publish_filtered_gps: true
  use_odometry_yaw: false
  wait_for_datum: false
```



## Map to Odom Node
This node will create a TF between the map link and the odometry link
```
<node pkg="robot_localization" type="ekf_localization_node" name="ekf_se_map" respawn="true">
  		<rosparam file="$(find ranger_navigation)/config/ekf_se_map.yaml" command="load" />
            <remap from="/odometry/filtered" to="<YOUR ODOM TOPIC>" />
  	</node>
```



Options:
```
frequency: 30
sensor_timeout: 0.1
two_d_mode: true
transform_time_offset: 0.0
transform_timeout: 0.0
print_diagnostics: true
debug: false

map_frame: map
odom_frame: odom
base_link_frame: base_footprint
world_frame: map

# -------------------------------------
# Wheel odometry:

odom0: /four_wheel_steering_controller/odom
odom0_config: [false, false, false,
               false, false, false,
               true,  true,  true,
               false, false, true,
               false, false, false]
odom0_queue_size: 10
odom0_nodelay: true
odom0_differential: false
odom0_relative: false

# -------------------------------------
# GPS odometry:

odom1: /odometry/gps                # THIS SHOULD THE NAVSAT GENERATED TOPIC (nav_msgs/Odometry)
odom1_config: [true,  true,  false,
               false, false, false,
               false, false, false,
               false, false, false,
               false, false, false]
odom1_queue_size: 10
odom1_nodelay: true
odom1_differential: false
odom1_relative: false

# -------------------------------------
# Laser scanmatching odometry:

#  odom2: scanmatch_odom
#  odom2_config: [false, false, false,
#                 false, false, false,
#                 true,  true,  true,
#                 false, false, true,
#                 false, false, false]
#  odom2_queue_size: 10
#  odom2_nodelay: true
#  odom2_differential: false
#  odom2_relative: false

# --------------------------------------
# imu configure:

imu0: /imu/data
imu0_config: [false, false, false,
              true,  true, false,
              false, false, false,
              true,  true,  true,
              true,  true,  true]
imu0_nodelay: true
imu0_differential: false
imu0_relative: false
imu0_queue_size: 10
imu0_remove_gravitational_acceleration: true

use_control: false

process_noise_covariance: [<MATRIX>]

initial_estimate_covariance: [<MATRIX>] 
```





## Odom to Base_link Node
This node will create a TF between the odometry link and the base link
```
<node pkg="robot_localization" type="ekf_localization_node" name="ekf_se_odom" respawn="true">
  		<rosparam file="$(find ranger_navigation)/config/ekf_se_odom.yaml" command="load" />
  	</node>
```



Options:
```
frequency: 30
sensor_timeout: 0.1
two_d_mode: true
transform_time_offset: 0.0
transform_timeout: 0.0
print_diagnostics: true
debug: false

map_frame: map
odom_frame: odom
base_link_frame: base_footprint
world_frame: odom

# -------------------------------------
# Wheel odometry:

odom0: /four_wheel_steering_controller/odom
odom0_config: [false, false, false,
               false, false, false,
               true,  true,  true,
               false, false, false,
               false, false, false]
odom0_queue_size: 10
odom0_nodelay: true
odom0_differential: false
odom0_relative: false

# -------------------------------------
# Laser scanmatching odometry:

#  odom1: scanmatch_odom
#  odom1_config: [false, false, false,
#                 false, false, false,
#                 true,  true,  true,
#                 false, false, true,
#                 false, false, false]
#  odom1_queue_size: 10
#  odom1_nodelay: true
#  odom1_differential: false
#  odom1_relative: false

# --------------------------------------
# imu configure:

imu0: /imu/data
imu0_config: [false, false, false,
              true,  true,  false,
              false, false, false,
              true,  true,  true,
              true,  true,  true]
imu0_nodelay: false
imu0_differential: false
imu0_relative: false
imu0_queue_size: 10
imu0_remove_gravitational_acceleration: true

use_control: false

process_noise_covariance: [<MATRIX>]

initial_estimate_covariance: [<MATRIX>] 
```

## More info
[Link 1](https://navigation.ros.org/setup_guides/odom/setup_odom.html)
