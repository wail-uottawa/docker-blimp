***A docker image to simulate lighter than air vehicles (LTAVs) in ROS/Gazebo***

**Maintainer:** *Wail Gueaieb*

[![Github All Releases](https://img.shields.io/github/downloads/wail-uottawa/Docker-Blimp/total.svg)]()
![GitHub last commit (branch)](https://img.shields.io/github/last-commit/wail-uottawa/Docker-Blimp/master)
[![GitHub license](https://img.shields.io/github/license/wail-uottawa/Docker-Blimp)](https://github.com/wail-uottawa/Docker-Blimp/blob/master/LICENSE)

<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**

- [Overview](#overview)
    - [ROS/Gazebo Installation](#rosgazebo-installation)
    - [Airship Simulation Installation](#airship-simulation-installation)
- [Getting the Docker Image](#getting-the-docker-image)
    - [Pulling the Docker Image from Docker Hub](#pulling-the-docker-image-from-docker-hub)
    - [Building the Docker Image Locally](#building-the-docker-image-locally)
- [Running the Container](#running-the-container)
    - [Connect & Control](#connect--control)
    - [Environment Settings](#environment-settings)
        - [Using root (user id `0`)](#using-root-user-id-0)
        - [Using user and group id of host system](#using-user-and-group-id-of-host-system)
        - [Overriding VNC and container environment variables](#overriding-vnc-and-container-environment-variables)
            - [Example: Overriding the VNC password](#example-overriding-the-vnc-password)
            - [Example: Overriding the VNC resolution](#example-overriding-the-vnc-resolution)
        - [Mounting a local directory to the container](#mounting-a-local-directory-to-the-container)
- [ROS Catkin Workspace](#ros-catkin-workspace)
- [Installation of Airship Simulation Repository](#installation-of-airship-simulation-repository)
- [Running Airship Simulations](#running-airship-simulations)
- [Acknowledgment](#acknowledgment)
- [Disclaimer](#disclaimer)

<!-- markdown-toc end -->



# Overview
This is a docker image aimed to help researchers and hobbyists to experiment with and simulate lighter than air vehicles (LTAVs) in ROS/Gazebo.

The image is composed of two main parts: (i) the installation of ROS/Gazebo, and (ii) the installation of the airship simulation stack, as described below.

## ROS/Gazebo Installation
This part is based on the docker image from the [henry2423/docker-ros-vnc](https://github.com/henry2423/docker-ros-vnc) repository. Most of the documentation for that image is still valid for this one, except:
  * Only ROS Melodic is supported here (ROS Kinetic and Lunar are not).
  * Tensorflow and Jupyter are not installed.
  * ROS installation is based on ros-melodic-desktop-full instead of ros-melodic-desktop. 
  * Few tweaks were necessary to go around a few generated errors while building the original docker image, such as those pertaining to installing Gazebo 9.
  
This yields  __Ubuntu 18.04 with `ROS Melodic + Gazebo 9`__

## Airship Simulation Installation
This part comes from the [Airship Simulation](https://github.com/robot-perception-group/airship_simulation) repository. More on this repository is included further down. 

# Getting the Docker Image
The docker image can be either pulled directly from Docker Hub, or built locally on your personal computer. The former method may be much more convenient. 

## Pulling the Docker Image from Docker Hub
Currently, the docker image lives in a Docker Hub repository [realjsk/docker-blimp](https://hub.docker.com/r/realjsk/docker-blimp "realjsk/docker-blimp"). It can be pulled using the docker command:

	docker pull realjsk/docker-blimp:20210323

## Building the Docker Image Locally
The source files to build the docker image on a local computer are stored at the Github repository [https://github.com/wail-uottawa/Docker-Blimp](https://github.com/wail-uottawa/Docker-Blimp).

1. Start by cloning the repository:  
   `git clone https://github.com/wail-uottawa/Docker-Blimp.git`
2. Then, cd to the directory including the file `Dockerfile` and (with the docker server running) build the image:  
   `docker build -t realjsk/blimp:20210323  .` (note the dot at the end)  
   If you want, you may change `realjsk/blimp` and `20210323` to any other image name and tag of your choice, respectively.  
   This can also be done by running the provided shell script `docker-build.sh` using the command:  
   `sh docker-build.sh`  
   Feel free to edit the shell script if you want.

# Running the Container
The container is developed under xfce-docker-container source, which makes it accessible through xfce-vnc or no-vnc (via http vnc service). In the following, it is assumed that the `name:tag` of the docker image is the same as the default one mentioned above (`realjsk/blimp:20210323`). If you used a different one, please make the necessary changes to the proceeding commands.

- Run command with a mapping to local port `5901` (vnc protocol) and `6901` (vnc web access):

      `docker run -d -p 5901:5901 -p 6901:6901 realjsk/blimp:20210323`

- Another alternative to connect to the container is to use the interactive mode `-it` and `bash`
      
      `docker run -it -p 5901:5901 -p 6901:6901 realjsk/blimp:20210323 bash`

## Connect & Control
Once it is running, you can connect to the container in a number of ways to be able to run GUI applications, such as Gazebo and Rviz:
* connect via __VNC viewer `localhost:5901`__, default password: `vncpassword`
* connect via __noVNC HTML5 full client__: [`http://localhost:6901/vnc.html`](http://localhost:6901/vnc.html), default password: `vncpassword` 
* connect via __noVNC HTML5 lite client__: [`http://localhost:6901/?password=vncpassword`](http://localhost:6901/?password=vncpassword) 

The default username and password in the container is `ros:ros`.

The default password for the `sudo` command is `ros`.

## Environment Settings

### Using root (user id `0`)
Add the `--user` flag to your docker run command. For example:

	docker run -it --user root -p 5901:5901 realjsk/blimp:20210323 bash

### Using user and group id of host system
In Unix-like host systems, you may add the `--user` flag to your docker run command. For example:

    docker run -it -p 5901:5901 --user $(id -u):$(id -g) realjsk/blimp:20210323

Note that it may not be always possible to map the user and group ids of the host system to those of the container, which is 1000:1000. In that case, you may want to try overriding the VNC and container envirenment variables, as explained below (see [Overriding VNC and container environment variables](#overriding-vnc-and-container-environment-variables)).

### Overriding VNC and container environment variables
The following VNC environment variables can be overwritten within the docker run command to customize the desktop environment inside the container:
* `VNC_COL_DEPTH`, default: `24`
* `VNC_RESOLUTION`, default: `1920x1080`
* `VNC_PW`, default: `vncpassword`
* `USER`, default: `ros`
* `PASSWD`, default: `ros`

#### Example: Overriding the VNC password
Simply overwrite the value of the environment variable `VNC_PW`. For example, in
the docker run command:

    docker run -it -p 5901:5901 -p 6901:6901 -e VNC_PW=vncpassword realjsk/blimp:20210323 

#### Example: Overriding the VNC resolution
Simply overwrite the value of the environment variable `VNC_RESOLUTION`. For example, in the docker run command:

    docker run -it -p 5901:5901 -p 6901:6901 -e VNC_RESOLUTION=800x600 realjsk/blimp:20210323

### Mounting a local directory to the container
Docker enables the mapping between directories on the host system and the container through the `--volume` directive. For example, the following command maps the host user/group with the container, and also maps a few directories between the host and the container. Note that in such cases, the host serves as the master while the container is the slave. With the following command, for instance, the user account in the container will be the same as the host account.

      docker run -it -p 5901:5901 \
        --user $(id -u):$(id -g) \
        --volume /etc/passwd:/etc/passwd \
        --volume /etc/group:/etc/group \
        --volume /etc/shadow:/etc/shadow \
        --volume /home/ros/Desktop:/home/ros/Desktop:rw \
        realjsk/blimp:20210323

You can learn more about volumes on this designated [docker reference page](https://docs.docker.com/storage/volumes/).

# ROS Catkin Workspace 
The container comes with a catkin workspace already set up. By default, the path for the catkin workspace is  
`/home/ros/catkin_ws`

Some ROS packages are installed in the catkin workspace, including the [Airship Simulation](https://github.com/robot-perception-group/airship_simulation) repository, as shall be described later.

In order for users to write their own ROS packages without running the risk of interfering with the pre-installed packages in this catkin workspace, it is recommended to include all user packages in a mapped directory inside the `src` directory of the catkin workspace. For example, the user may want to dedicate a folder on the host machine for his own ROS packages. Let's say that the path to this folder is `/home/john/work_dir`, then it can be mapped to a folder inside the container's catkin workspace by adding the following part to the docker run command:  
`--volume /home/john/work_dir:/home/ros/catkin_ws/src/work_dir:rw`

Be aware that you may have to specify the path to the host folder differently in case the host is running Windows as an operating system.

# Installation of Airship Simulation Repository
If the docker image was pulled from the Docker Hub, as explained in [Pulling the Docker Image from Docker Hub](#pulling-the-docker-image-from-docker-hub) Section, then the [Airship Simulation](https://github.com/robot-perception-group/airship_simulation) repository comes pre-installed and configured. 

However, in case the image was built locally, as described in the [Building the Docker Image Locally](#building-the-docker-image-locally) Section, then the [Airship Simulation](https://github.com/robot-perception-group/airship_simulation) repository comes only partially installed. To complete the installation, Step 5 ("To build the LibrePilot Submodule, go into the LibrePilot subfolder") of the "Installation Instructions - Ubuntu 18.04 with ROS Melodic and Gazebo 9" [Section](https://github.com/robot-perception-group/airship_simulation#installation-instructions---ubuntu-1804-with-ros-melodic-and-gazebo-9) needs to be applied manually from within the container. To do so, tt is recommended to follow the steps below, which represent a slightly modified version of the original procedure descriped on the [Airship Simulation](https://github.com/robot-perception-group/airship_simulation) Github page. 

1. cd to the catkin workspace
> `cd ~/catkin_ws`
2. cd to the LibrePilot directory
> `cd src/airship_simulation/LibrePilot`
3. Install qt sdk for building the GCS
> `make qt_sdk_install`
4. Install arm sdk for building the flightcontroller firmware
> `make arm_sdk_install`
5. Install uncrustify
> `make uncrustify_install`
6. Install build dependencies
> `sudo apt update`  
> `sudo apt install -y libopenscenegraph-3.4-dev`  
> `sudo apt install -y libusb-dev libsdl-dev libudev-dev libosgearth-dev ros-melodic-mav-comm`
7. Build gcs
> `make -j 10 gcs`  
If this fails, check the error messages for possible additional dependencies.
8. Build SITL flightcontroller executable
> `make -j 10 fw_simposix`
9. Build HITL flightcontroller firmware
> `make -j 10 fw_revolution`

The last step will most likely fail (see the [Airship Simulation](https://github.com/robot-perception-group/airship_simulation) Github page for details). However, it is only useful for the hardware flightcontroller. If you are only interested in ROS/Gazebo simulations, it is not required. Note that the hardware flightcontroller is not built in the Docker Hub image either. 

After that, follow the instructions in the "Basic Usage" [Section](https://github.com/robot-perception-group/airship_simulation#basic-usage) to set the required configuration of the GCS. Note that this step is not necessary if you are using the Docker Hub image as it is already done for you.

To make these changes permanent in your locally built image, run the following command at a host shell (not within the container):

	docker commit RUNNING_CONTAINER_ID NEW_IMAGE_NAME

where `RUNNING_CONTAINER_ID` is the ID of the running container (which you can get by running `docker container ls` within a host shell) and `NEW_IMAGE_NAME` is the name you want to give to the new image which reflects the current state of your container. You can learn more about this command on this [docker reference page](https://docs.docker.com/engine/reference/commandline/commit/).

# Running Airship Simulations
Once the docker image is properly set up, you should be able to run airship simulations in ROS/Gazebo, as described in the [Airship Simulation](https://github.com/robot-perception-group/airship_simulation) repository. Please refer to that repository for technical details on how to do so. 

# Acknowledgment
Credit goes primarily to the maintainers of the following projects:

* [henry2423/docker-ros-vnc](https://github.com/henry2423/docker-ros-vnc)
* [Airship Simulation](https://github.com/robot-perception-group/airship_simulation)

# Disclaimer
The main purpose of this repository and docker image is to facilitate researchers efforts in conducting realistic simulations of LTAVs. However, they come with no warranty. Please use them at your own discretion. 

I am no docker expert. It is very likely that the generated docker image and the provided `Dockerfile` are by no means optimal. 
