# 1. Install ROS noetic

## Setup computer to accept software from packages.ros.org.
```bash
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
```

## Set up your keys
```bash
sudo apt install curl # if you haven't already installed curl
curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
```

## Installation
```bash
sudo apt update
sudo apt install ros-noetic-desktop-full
```

## Environment setup
```bash
source /opt/ros/noetic/setup.bash
```
It can be convenient to automatically source this script every time a new shell is launched. These commands will do that for you.
```bash
echo "source /opt/ros/noetic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

## Dependencies for building packages
```bash
sudo apt install python3-rosdep python3-rosinstall python3-rosinstall-generator python3-wstool build-essential
```

Initialize rosdep
```bash
sudo apt install python3-rosdep
sudo rosdep init
rosdep update
```

# ROS tutorial

## Create a ROS Workspace
```bash
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/
catkin_make
```

Sourcing any of these files will overlay this workspace on top of your environment. To understand more about this see the general catkin documentation: catkin. Before continuing source your new setup.*sh file:
```bash
source devel/setup.bash
```

To make sure your workspace is properly overlayed by the setup script, make sure ROS_PACKAGE_PATH environment variable includes the directory you're in.
```bash
echo $ROS_PACKAGE_PATH
```
# 2. Run simulation MPO-500
## Clone packages from github
```bash
cd ~/arm_ros/catkin_ws/src
```
[Packages for Simulation](https://neobotix-docs.de/ros/ros1/installation.html#)
```bash
git clone https://github.com/neobotix/neo_mpo_500.git
git clone https://github.com/neobotix/neo_simulation.git
git clone https://github.com/neobotix/neo_kinematics_mecanum
git clone https://github.com/neobotix/neo_relayboard_v2
git clone https://github.com/neobotix/neo_sick_s300.git
git clone https://github.com/neobotix/neo_teleop.git
git clone https://github.com/neobotix/neo_msgs.git
git clone https://github.com/neobotix/neo_srvs.git
git clone https://github.com/neobotix/neo_common.git
git clone https://github.com/tu-darmstadt-ros-pkg/hector_slam.git
```
Navigation Packages
```bash
git clone https://github.com/ros-perception/slam_gmapping.git
git clone https://github.com/neobotix/neo_localization.git
```
Other dependencies
```bash
sudo apt install ros-$ROS_DISTRO-joy ros-$ROS_DISTRO-tf2-sensor-msgs ros-$ROS_DISTRO-tf2-geometry-msgs
```
# 3. Run simulation SLAM-TEC

## Build packages
```bash
cd ~/arm_ros/catkin_ws/src
git clone https://github.com/Slamtec/rplidar_ros.git
cd ..
catkin_make
source devel/setup.bash
```

## Add permission
```bash
sudo chmod 666 /dev/ttyUSB0
```

## Install dependence packages
```bash
sudo add-apt-repository ppa:rock-core/qt4
sudo apt-get update
sudo apt-get install libqtcore4
sudo apt-get install qt4-qmake qt4-dev-tools
```

## Set the coordinate frame parameter (if be not yet setted)
```bash
sudo gedit ~/catkin_ws/src/hector_slam/hector_mapping/launch/mapping_default.launch
```

Search for these lines (lines 5 and 6 in my code).

```html
	<arg name="base_frame" default="base_footprint"/>
	<arg name="odom_frame" default="nav"/>
```
	
Change those lines to this:
```html
	<arg name="base_frame" default="base_link"/>
	<arg name="odom_frame" default="base_link"/>
```
	
Now go to the end of this file, and find these lines (line 54 in my code).
	<!--<node pkg="tf" type="static_transform_publisher" name="map_nav_broadcaster" args="0 0 0 0 0 0 map nav 100"/>-->
	
Change those lines to this (be sure to remove the comment tags (<!–- and -–>):
	<node pkg="tf" type="static_transform_publisher" name="base_to_laser_broadcaster" args="0 0 0 0 0 0 base_link laser 100"/>
	
Save the file, and return to the terminal window.

Type the following command.

```bash
cd ~/catkin_ws/src/hector_slam/hector_slam_launch/launch
gedit tutorial.launch
```

Find this line (line 7 in my code).
```html
	<param name="/use_sim_time" value="true"/>
```

Change that line to:
```html
	<param name="/use_sim_time" value="false"/>
```

Save the file, and close it.

```bash
cd ~/catkin_ws/
catkin_make
source devel/setup.bash
```

## Run simulation
NOTE: Each terminal needs to be re-run the following command to be able to use roslaunch.
```bash
source devel/setup.bash
```
```bash
roscore # termial 1
roslaunch rplidar_ros view_rplidar_s2.launch # terminal 2
roslaunch hector_slam_launch tutorial.launch # terminal 3
```
# 4. Install ODrivetool and GUI
## Install Odrivetool
Install python3 and pip3 (If you already have python3 installed for ROS, just install pip3)
```bash
sudo apt-get install python3 python3-pip
```
Install odrivetool by opening a ternimal and typing
```bash
sudo pip install --upgrade odrive
```
Start Odrive
```
odrivetool
```

## Build OdriveGUI
Clone repository
```bash
git clone https://github.com/odriverobotics/ODrive.git
```
Move GUI
```bash
cd ~/ODrive/GUI
```
Install NodeJS
```bash
sudo apt install nodejs
```

If version is old, update version NodeJS
```bash
sudo npm cache clean -f
sudo npm install -g n
sudo n stable
```

Install requirement packages
```bash
sudo npm install
```

Build project
```bash
Crlt + Shift + B
```

# 5. Reference

[BUILD MAP](https://automaticaddison.com/how-to-build-an-indoor-map-using-ros-and-lidar-based-slam/)

[BUILD MAP](https://www.hackster.io/shahizat005/building-a-map-using-lidar-with-ros-melodic-on-jetson-nano-2f92dd#toc-step-1---flash-the-image-to-the-sd-card-and-boot-it-up-3)

[VIDEO BUILD MAP](https://www.youtube.com/watch?v=IH_n9bfy-nM)

[SLAM TEC](https://www.slamtec.com/en/Support?spm=a2700.12243863.0.0.3e5e4539AqzBYo#rplidar-s2)

[ODrive GUI](https://github.com/odriverobotics/ODrive/tree/master/GUI)