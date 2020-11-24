[![Build Status](https://travis-ci.org/amc-nu/HesaiLidar_Pandar64_ros.svg?branch=master)](https://travis-ci.org/amc-nu/HesaiLidar_Pandar64_ros)

# HesaiLidar_General_ROS

## About the project
HesaiLidar_General_ROS project includes the ROS Driver for：  
**PandarQT/Pandar64/Pandar40P/Pandar20A/Pandar20B/Pandar40M/PandarXT**  
LiDAR sensor manufactured by Hesai Technology.  

Developed based on [HesaiLidar_General_SDK](https://github.com/HesaiTechnology/HesaiLidar_General_SDK), After launched, the project will monitor UDP packets from Lidar,     parse data and publish point cloud frames into ROS under topic: ```/pandar```. It can also be used as an official demo showing how to work with HesaiLidar_General_SDK.

## Environment and Dependencies
**System environment requirement: Linux + ROS**  

　Recommanded:  
　Ubuntu 16.04 - with ROS kinetic desktop-full installed or  
　Ubuntu 18.04 - with ROS melodic desktop-full installed  
　Check resources on http://ros.org for installation guide 
 
**Library Dependencies: libpcap-dev + libyaml-cpp-dev**  
```
$sudo apt install libpcap-dev libyaml-cpp-dev
```

## Download and Build

**Install `catkin_tools`**
```
$ sudo apt-get update
$ sudo apt-get install python-catkin-tools
```
**Download code**  
```
$ mkdir -p rosworkspace/src
$ cd rosworkspace/src
$ git clone https://github.com/HesaiTechnology/HesaiLidar_General_ROS.git --recursive
```
**Build**
```
$ cd ..
$ catkin config --install
$ catkin build --force-cmake
```

## Configuration 
```
 $ gedit install/share/hesai_lidar/launch/hesai_lidar.launch
```
**Reciving data from connected Lidar: config lidar ip&port, leave the pcap_file empty**
|Parameter | Default Value|
|---------|---------------|
|server_ip |192.168.1.201|
|lidar_recv_port |2368|
|gps_recv_port  |10110|
|pcap_file ||　　

Data source will be from connected Lidar when "pcap_file" set to empty
Make sure parameters above set to the same with Lidar setting

**Reciving data from pcap file: config pcap_file and correction file path**
|Parameter | Value|
|---------|---------------|
|pcap_file |pcap file path|
|lidar_correction_file |lidar correction file path|　

Data source will be from pcap file once "pcap_file" not empty 


## Run

1. Make sure current path in the `rosworkspace` directory
```
$ source install/setup.bash
```
```
for PandarQT
$ roslaunch hesai_lidar hesai_lidar.launch lidar_type:="PandarQT"
for Pandar64
$ roslaunch hesai_lidar hesai_lidar.launch lidar_type:="Pandar64"
for Pandar20A
$ roslaunch hesai_lidar hesai_lidar.launch lidar_type:="Pandar20A"
for Pandar20B
$ roslaunch hesai_lidar hesai_lidar.launch lidar_type:="Pandar20B"
for Pandar40P
$ roslaunch hesai_lidar hesai_lidar.launch lidar_type:="Pandar40P"
for Pandar40M
$ roslaunch hesai_lidar hesai_lidar.launch lidar_type:="Pandar40M"
for PandarXT-32
$ roslaunch hesai_lidar hesai_lidar.launch lidar_type:="PandarXT-32"
for PandarXT-16
$ roslaunch hesai_lidar hesai_lidar.launch lidar_type:="PandarXT-16"
```
2. The driver will publish PointCloud messages to the topic `/pandar`  
3. Open Rviz and add display by topic  
4. Change fixed frame to lidar_type to view published point clouds  

## Details of launch file parameters and utilities
|Parameter | Default Value|
|---------|---------------|
|pcap_file|Path of the pcap file, once not empty, driver will get data from pcap file instead of a connected Lidar|
|server_ip|The IP address of connected Lidar, will be used to get calibration file|
|lidar_recv_port|The destination port of Lidar, driver will monitor this port to get point cloud packets from Lidar|
|gps_port|The destination port for Lidar GPS packets, driver will monitor this port to get GPS packets from Lidar|
|start_angle|Driver will publish one frame point cloud data when azimuth angel step over start_angle, make sure set to within FOV|
|lidar_type|Lidar module type, will be used also as frame id|
|pcldata_type|0:mixed point cloud data type  1:structured point cloud data type|
|publish_type|default "points":publish point clouds "raw":publish raw UDP packets "both":publish point clouds and UDP packets|
|timestamp_type|default "": use timestamp from Lidar "realtime" use timestamp from the system  driver running on|
|data_type|default "":driver will get point cloud packets from Lidar or PCAP "rosbag":driver will subscribe toic /pandar_packets to get point cloud packets|
|lidar_correction_file|Path of correction file, will be used when not able to get correction file from a connected Liar|
### Publish raw UDP packets to ROS
set "publish_model" to "raw" or "both" then launch driver    
The driver will publish a raw data packet message in the topic
```
/pandar_packets
```
### Record and parse raw UDP packets from ROS topic
1. record raw data rosbag
```
$rosbag record -b 4096 /pandar_packets
```
2. stop rosbag record by "Ctrl + C", a rosbag file will be generated in the terminal working path

3. play raw data rosbag
`
$rosbag play <rosbagfile>
`
    this will publish raw udp packets to ROS under the topic `/pandar_packets`    
4. set "data_type" in launch file to "rosbag", set correct "lidar_type", launch driver
```
$ roslaunch hesai_lidar hesai_lidar.launch
```
5. Driver will get data from the `/pandar_packets` topic, parse and publish point cloud to `/pandar_points`, open Rviz and add display by topic.
6. Change fixed frame to lidar_type to view published point clouds

