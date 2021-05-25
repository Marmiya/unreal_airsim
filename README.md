# unreal_airsim
This repo contains simulation tools and utilities to perform realistic simulations base on [Unreal Engine](https://www.unrealengine.com/en-US/) (UE4), using microsoft [AirSim](https://github.com/microsoft/AirSim) as interface to UE4.

![preview](https://user-images.githubusercontent.com/36043993/82642589-7551ed00-9c0e-11ea-99b6-fab22fcff837.png)
 
# Table of Contents
**Getting started:**
* [Installation](#Instalation)
* [Contributing](#Contributing)
* [Examples](#Examples)
* [Troubleshooting](#Troubleshooting)

**Documentation:**
* [Settings](docs/settings.md)
* [Coordinate Systems](docs/coordinate_systems.md)
* [Getting UE4 Assets](docs/download_ue4_assets.md)
* [Tips and Tricks](docs/tips_and_tricks.md)

# Installation
The following 3 components are necessary to utilize the full stack of unreal_airsim tools.

**Unreal Engine**

Install Unreal Engine. This repository was developped and tested on UE 4.24.3, which is the recommended version.
To install UE4 on linux, you need to register with Epic Games and build it from source. 
Please follow the detailed instructions on [their website](https://docs.unrealengine.com/en-US/Platforms/Linux/BeginnerLinuxDeveloper/SettingUpAnUnrealWorkflow/index.html) to set everything up.
If you plan to use *only* pre-compiled binaries as simulation worlds, this section can be omitted,

**Airsim**

Install *our fork* of AirSim, the UE4 Plugin:
```shell script
cd </where/to/install>
git clone git@github.com:ethz-asl/AirSim.git
cd Airsim
./setup.sh 
./build.sh
```

**unreal_airsim**

Install unreal_airsim, containing the simulation ROS-package and tools.

* If you haven't already installed ROS, please install it according to their instructions. 
This repo was developed on a desktop-full version of [ROS melodic](http://wiki.ros.org/melodic/Installation/Ubuntu/).

* System dependencies:
    ```shell script
    sudo apt-get install python-wstool python-catkin-tools ros-melodic-cmake-modules
    ```
 
* If you haven't already set up a caktin worskpace:
    ```shell script
    mkdir -p ~/catkin_ws/src
    cd ~/catkin_ws
    catkin init
    catkin config --extend /opt/ros/melodic
    catkin config --cmake-args -DCMAKE_BUILD_TYPE=Release
    catkin config --merge-devel
    ```

* Install via [SSH](https://help.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh):
    ```shell script
    cd ~/catkin_ws/src
    git clone git@github.com:ethz-asl/unreal_airsim.git
    wstool init . ./unreal_airsim/unreal_airsim_ssh.rosinstall
    wstool update
    ```
* Tell `unreal_airsim` where you installed AirSim by running:
    ```shell script
    cd ~/catkin_ws/src/unreal_airsim
    echo "set(AIRSIM_ROOT $HOME/catkin_ws/src/AirSim)" > ./AirsimPath.txt
    ```
  In case you didn't install AirSim in your `~/catkin_ws/src` folder, don't forget to replace the above `$HOME/catkin_ws/src/AirSim` path with the path to the alternative location your chose.

* Build:
    ```shell script
    catkin build unreal_airsim
    source ../devel/setup.bash
    ```
  
# Contributing
If you are adding features to this repo please consider opening back a PR, so others can use it as well.
If you consider contributing, please adhere to the [google style guide](https://google.github.io/styleguide/cppguide.html) and setup our linter:
```shell script
# Download the linter
cd <linter_dest>
git clone git@github.com:ethz-asl/linter.git  # SSH
git clone https://github.com/ethz-asl/linter.git  # HTTPS

# install the linter
cd linter
echo ". $(realpath setup_linter.sh)" >> ~/.bashrc  # Or the matching file for
                                                   # your shell.
bash

# Register this repo for the linter
roscd unreal_airsim
init_linter_git_hooks
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

![u_airsim_4](https://user-images.githubusercontent.com/36043993/79876617-90e98e00-83eb-11ea-8edb-f11156a716d1.png)

External view of the UE4 game (left), camera image (center) and lidar readings (right) obtained from the simulator.

# Troubleshooting

## Installation
* **Include error 'xlocale.h" not found:**

    xlocale was removed/renamed from glibc somewhen, can fix via symlink:
    ```shell script
     ln -s /usr/include/locale.h /usr/include/xlocale.h
    ```
  
## Running Unreal Engine
* **The UE Editor freezes when 'Compiling Shaders':**

    According to [this thread](https://answers.unrealengine.com/questions/936174/view.html) this may happen when using Vulcan with less than 2GB of graphics card memory. 
    Can be adjusted by switching back to OpenGL (uncomment `TargetedRHIs=GLSL_430` in `Engine/Config/BaseEngine.ini`). 

## Starting the simulation
* **Error at startup: bind(): address already in use:**

    Airsim connects to UE4 via a TCP port. If multiple instances of Airsim (i.e. the unreal game) are running, the selected port will already be taken.
    Note that the editor itself already loads the Airsim plugin, thus closing all instances of UE4 (editor or game) and starting a single game/editor usually fixes this.



