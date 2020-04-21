# unreal_airsim
This repo contains simulation tools and utilities to perform realistic simulations base on [Unreal Engine](https://www.unrealengine.com/en-US/) (UE4), using microsoft [AirSim](https://github.com/microsoft/AirSim) as interface to UE4.
 
# Table of Contents
**Getting started:**
* [Installation](#Instalation)
* [Examples](#Examples)
* [Troubleshooting](#Troubleshooting)

**Documentation:**
* coming soon

# Installation
The following 3 components are necessary to utilize the full stack of unreal_airsim tools.

**Unreal Engine**

Install Unreal Engine. This repository was developped and tested on UE 4.24, which is the recommended version.
To install UE4 on linux, you need to register with Epic Games and build it from source. 
Please follow the detailed instructions on [their website](https://docs.unrealengine.com/en-US/Platforms/Linux/BeginnerLinuxDeveloper/SettingUpAnUnrealWorkflow/index.html) to set everything up.
If you plan to use *only* pre-compiled binaries as simulation worlds, this section can be omitted,

**Airsim**

Install *our fork* of AirSim, the UE4 Plugin:
```shell script
cd </where/to/install>
git clone https://github.com/ethz-asl/AirSim.git
cd Airsim
./setup.sh 
./build.sh
```

**unreal_airsim**

Install unreal_airsim, containing the simulation ROS-package and tools.

If you haven't installed ROS and setup a catkin workspace:
```shell script
#TODO: look at another repo how to set it up. This was developed and tested on Ubuntu 18.04 with ROS melodic-desktop-full.
```

Dependencies:

No special system dependencies required at the moment.
```shell script
#sudo apt-get install TODO-Doublecheck
#pip install TODO2
```

Install:
```shell script
cd ~/catkin_ws/src
git clone https://github.com/ethz-asl/unreal_airsim.git
cd unreal_airsim
```
In `CMakeLists.txt`, change the airsim root path in line 7 to your install directory `set(AIRSIM_ROOT /where/to/install/AirSim)`.
```shell script
catkin build --this
source ../../devel/setup.bash
```
# Examples
This demo briefly walks through the steps on how to use the online_simulator.
The simulation is setup from a single configuration file, a minimal example is given in `cfg/demo.yaml`.
The settings required for AirSim to produce the requested simulation must first be generated by running
```shell script
roslaunch unreal_airsim parse_config_to_airsim.launch 
```
It is strongly recommended to produce AirSim's Settings.json using this script and not changing it manually.
Additional AirSim Settings can be set in the yaml-config using *CamelCase* params with identical names.

In this demo, start the `Blocks` environment provided by AirSim, and run the game in the UE4 Editor (select 'Active Play Mode' = 'Selected Viewport', then 'Play').
Then run 
```shell script
roslaunch unreal_airsim demo.launch 
```
to start the simulation and display the sensor readings in RVIZ.

# Troubleshooting

## Installation
* **Include error 'xlocale.h" not found:**

    xlocale was removed/renamed from glibc somewhen, can fix via symlink:
    ```shell script
     ln -s /usr/include/locale.h /usr/include/xlocale.h
    ```
## Starting the simulation
* **Error at startup: bind(): address already in use:**

    Airsim connects to UE4 via a TCP port. If multiple instances of Airsim (i.e. the unreal game) are running, the selected port will already be taken.
    Note that the editor itself already loads the Airsim plugin, thus closing all instances of UE4 (editor or game) and starting a single game/editor usually fixes this.



