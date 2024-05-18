
# Drone Simulation using `Dronekit` in Ubuntu 22



This documented text helps you setup Ardupilot SITL with Gazebo Sim 7 in Ubuntu 22. This is an accumulated documentation on installing the above mentioned software. The original documentations from which this is derived are [Using SITL with Gazebo](https://ardupilot.org/dev/docs/sitl-with-gazebo.html) and [Setting up the Build Environment (Linux/Ubuntu)](https://ardupilot.org/dev/docs/building-setup-linux.html#setting-up-the-build-environment-linux-ubuntu "Link to this heading")

This documentation is specially curated with love from the `Aero Fabrication Club` and `Electronics and Robotics Society` of **PDPM IIITDMJ**.

## Objective
To install, setup and simulate Ardupilot SITL with Gazebo Sim 7 in Ubuntu 22.
Here are few resources if you wanna dig deep in how the things work:
- [What is the Simulation that we are about to setup?](https://ardupilot.org/dev/docs/simulation-2.html#simulation "Link to this heading")
- [Using SITL with Gazebo](https://ardupilot.org/dev/docs/sitl-with-gazebo.html#using-sitl-with-gazebo "Link to this heading")
- [What is SITL?](https://ardupilot.org/dev/docs/sitl-simulator-software-in-the-loop.html)


## Prerequisites
Users must have:
 - `Git` installed in their Ubuntu 22.

    `sudo apt update`
    `sudo apt install git`
    
## ArduPilot Setup

### 1. Clone ArduPilot repository
    cd
    git clone --recurse-submodules https://github.com/ArduPilot/ardupilot
    cd ardupilot

### 2. Install some required packages
If you are on a debian based system (such as Ubuntu or Mint), there is [a script](https://github.com/ArduPilot/ardupilot/blob/master/Tools/environment_install/install-prereqs-ubuntu.sh) that will do it for you. From the cloned ardupilot directory :

- `Tools/environment_install/install-prereqs-ubuntu.sh -y`

Reload the path (log-out and log-in to make it permanent):

- `. ~/.profile`

Now you should be able to build with waf as described in [BUILD.md](https://github.com/ArduPilot/ardupilot/blob/master/BUILD.md).

### Cleaning[¶](https://ardupilot.org/dev/docs/building-setup-linux.html#cleaning "Link to this heading")

If there have been updates to some git submodules you may need to do a full clean build. To do that use:

- `./waf clean`

That will remove the build artifacts so you can do a [build](https://github.com/ArduPilot/ardupilot/blob/master/BUILD.md) from scratch

## Using SITL with Gazebo
We will be using Gazebo Garden (aka Gazebo Sim 7) as it is officially supported by ArduPilot's SITL.

### 1. Installing Gazebo Garden (Gazebo Sim 7)
First install some necessary tools:

    sudo apt-get update
    sudo apt-get install lsb-release wget gnupg

Then install Gazebo Garden:

    sudo wget https://packages.osrfoundation.org/gazebo.gpg -O /usr/share/keyrings/pkgs-osrf-archive-keyring.gpg
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/pkgs-osrf-archive-keyring.gpg] http://packages.osrfoundation.org/gazebo/ubuntu-stable $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/gazebo-stable.list > /dev/null
    sudo apt-get update
    sudo apt-get install gz-garden

Check that the Gazebo installation is working:
  
	gz sim -v4 -r shapes.sdf

If you see the following window and no errors in the terminal, you have installed **Gazebo Sim 7** correctly.

![enter image description here](https://api.gazebosim.org/1.0/images/harmonic/tutorials/gui/shapes.png)
### 2. Install additional dependencies
For Gazebo garden
	
	sudo apt update
	sudo apt install libgz-sim7-dev rapidjson-dev

### 3. Clone and build the `ardupilot_gazebo` repository

    cd
    git clone https://github.com/ArduPilot/ardupilot_gazebo
    cd ardupilot_gazebo
    mkdir build && cd build
    cmake .. -DCMAKE_BUILD_TYPE=RelWithDebInfo
    make -j4

### 
