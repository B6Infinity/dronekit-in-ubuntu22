
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
 - Git installed in their Ubuntu 22
 
		sudo apt update
		sudo apt install git
    
## ArduPilot Setup

### 1. Clone ArduPilot repository
    cd
    git clone --recursive https://github.com/ArduPilot/ardupilot
    cd ardupilot

### 2. Install some required packages
If you are on a debian based system (such as Ubuntu or Mint), there is [a script](https://github.com/ArduPilot/ardupilot/blob/master/Tools/environment_install/install-prereqs-ubuntu.sh) that will do it for you. From the cloned ardupilot directory :

	Tools/environment_install/install-prereqs-ubuntu.sh -y

Reload the path (log-out and log-in to make it permanent):

	. ~/.profile

Now you should be able to build with waf as described in [BUILD.md](https://github.com/ArduPilot/ardupilot/blob/master/BUILD.md).

### Cleaning[¶](https://ardupilot.org/dev/docs/building-setup-linux.html#cleaning "Link to this heading")

If there have been updates to some git submodules you may need to do a full clean build. To do that use:

	./waf clean

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

If you see the following window and no errors in the terminal, you have installed **Gazebo Sim 7** correctly. Use the left, right and middle mouse button to interact with the viewport.

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

### 4. Adding required environment variables
Open the `~/.bashrc` file:
	
	gedit ~/.bashrc
Add the following lines to the end of your .bashrc file and save the file.

	export simvehicle_path="$HOME/ardupilot/Tools/autotest/sim_vehicle.py"
	export ARDUPILOT_HOME="$HOME/ardupilot"
	export GZ_SIM_RESOURCE_PATH="$GZ_SIM_RESOURCE_PATH:$HOME/ardupilot_gazebo/worlds:$HOME/ardupilot_gazebo/models
	export GZ_SIM_SYSTEM_PLUGIN_PATH="$GZ_SIM_SYSTEM_PLUGIN_PATH:$HOME/ardupilot_gazebo/build" 

### 5. Run Gazebo
Open the `iris_runaway.sdf` file on Gazebo

	gz sim -v4 -r iris_runway.sdf

If everything goes correctly, you should see the following world load up in Gazebo Sim 7:
![enter image description here](https://github.com/B6Infinity/dronekit-in-ubuntu22/blob/main/image.png?raw=true) 


### 6. Run the SITL

	python3 $simvehicle_path -v ArduCopter -f gazebo-iris --model JSON --map --console

You should see two terminals pop-up. If you see the text `Flight battery 100 percent` in the parent terminal and

> JSON received: 	timestamp 	
> imu: gyro 	
> imu: accel_body 	
> position
> quaternion
> velocity

in the pop-up terminal, you are good to go.

However there is a problem. The `--console` flag is supposed to open up a mavlink console, but it fails for some reason, I will update this once I find the fix.

## Run `Dronekit` code

### 1. Install `Dronekit` for `python3`

	pip3 install dronekit

A common error is that when `import dronekit` is executed, it throws the error

> AttributeError: module 'collections' has no attribute 'MutableMapping'

To fix this:
- Open the `__init__.py` file of the `dronekit` package (In an IDE like VS-Code, type in `import dronekit` and Ctrl+Click on **dronekit**
- Add the following lines manually after the import collections statement

	    import collections
  
	    # Manual Patch
	    import collections
	    from collections import abc
	    collections.MutableMapping = abc.MutableMapping

* However, this is a quick fix and not recommended in the long run, visit [this forum](https://stackoverflow.com/questions/70943244/attributeerror-module-collections-has-no-attribute-mutablemapping) to understand the problem.


### 2. Create a `DroneTerminal.py` file anywhere and put in the [following code](https://github.com/B6Infinity/dronekit-in-ubuntu22/blob/main/DroneTerminal.py)

### 3. Create a `pulse.py` file and put in the [following code](https://github.com/B6Infinity/dronekit-in-ubuntu22/blob/main/pulse.py)

The Drone should takeoff, hold altitude for a few seconds and come down.

-----------------

## What next?

Visit the [documentation](https://dronekit-python.readthedocs.io/en/latest/) of `Dronekit` and have fun.

