Docker-Blimp
============

***A docker image to simulate lighter than air vehicles (LTAVs) in ROS/Gazebo***

**Author:** *Wail Gueaieb*


<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**

- [Docker-Blimp](#docker-blimp)
    - [Current Image Build:](#current-image-build)
    - [Spec](#spec)
    - [Usage](#usage)
    - [Connect & Control](#connect--control)
    - [Detail Environment setting](#detail-environment-setting)
        - [-](#-)
        - [1.2) Using user and group id of host system](#12-using-user-and-group-id-of-host-system)
        - [2) Override VNC and Container environment variables](#2-override-vnc-and-container-environment-variables)
            - [2.1) Example: Override the VNC password](#21-example-override-the-vnc-password)
            - [2.2) Example: Override the VNC resolution](#22-example-override-the-vnc-resolution)
        - [3) Mounting local directory to conatiner](#3-mounting-local-directory-to-conatiner)
    - [Installed Robots](#installed-robots)
        - [Fixed Manipulators](#fixed-manipulators)
        - [Wheeled Mobile Robots](#wheeled-mobile-robots)
        - [Wheeled Mobile Manipulators](#wheeled-mobile-manipulators)
        - [Aerial robots](#aerial-robots)
    - [Contributors](#contributors)

<!-- markdown-toc end -->

This repository main developed from henry2423/docker-ros-vnc: [https://github.com/henry2423/docker-ros-vnc](https://github.com/henry2423/docker-ros-vnc). Most of the documentation for that repository is valid for this one, except:
  * Only ROS Melodic is supported here (ROS Kinetic and Lunar are not).
  * Tensorflow and Jupyter are not installed.
  * ROS-melodic-desktop-full is used instead of the original version.
 
## Current Image Build:
* `henry2423/ros-vnc-ubuntu:melodic`: __Ubuntu 18.04 with `ROS Melodic + Gazebo 9`__

  [![](https://images.microbadger.com/badges/version/henry2423/ros-vnc-ubuntu:melodic.svg)](https://hub.docker.com/r/henry2423/ros-vnc-ubuntu/) [![](https://images.microbadger.com/badges/image/henry2423/ros-vnc-ubuntu:melodic.svg)](https://microbadger.com/images/henry2423/ros-vnc-ubuntu:melodic)

* `wail/docker-ros-elg5228:latest`: __Ubuntu 18.04 with `ROS Melodic + Gazebo 9`__

  ![GitHub all releases](https://img.shields.io/github/downloads/atom/atom/total)
  ![Docker Image Size (tag)](https://img.shields.io/docker/image-size/henry2423/ros-vnc-ubuntu/melodic)
  ![Docker Image Version (tag latest semver)](https://img.shields.io/docker/v/henry2423/ros-vnc-ubuntu/melodic)

## Spec
This is a Docker environmentalist equipped with ROS, Gazebo, xfce-vnc, and no-vnc(http vnc service).
The container is developed under xfce-docker-container source where ROS is added on top of it. Such an environment provides enough power and flexibility for teaching robotic courses. 

## Usage
- Run command with mapping to local port `5901` (vnc protocol) and `6901` (vnc web access):

      docker run -d -p 5901:5901 -p 6901:6901 henry2423/ros-vnc-ubuntu:kinetic

- If you want to get into the container use interactive mode `-it` and `bash`
      
      docker run -it -p 5901:5901 -p 6901:6901 henry2423/ros-vnc-ubuntu:kinetic bash

- Build an image from scratch:

      docker build -t henry2423/ros-vnc-ubuntu:kinetic .

## Connect & Control
If the container runs up, you can connect to the container throught the following 
* connect via __VNC viewer `localhost:5901`__, default password: `vncpassword`
* connect via __noVNC HTML5 full client__: [`http://localhost:6901/vnc.html`](http://localhost:6901/vnc.html), default password: `vncpassword` 
* connect via __noVNC HTML5 lite client__: [`http://localhost:6901/?password=vncpassword`](http://localhost:6901/?password=vncpassword) 
* connect to __Tensorboard__ if you do the tensorboard mapping above: [`http://localhost:6006`](http://localhost:6006)
* The default username and password in container is ros:ros

## Detail Environment setting

#### 1.1) Using root (user id `0`)
Add the `--user` flag to your docker run command:

    docker run -it --user root -p 5901:5901 henry2423/ros-vnc-ubuntu:kinetic

#### 1.2) Using user and group id of host system
Add the `--user` flag to your docker run command (Note: uid and gui of host system may not able to map with container, which is 1000:1000. If that is the case, check with 3):

    docker run -it -p 5901:5901 --user $(id -u):$(id -g) henry2423/ros-vnc-ubuntu:kinetic

### 2) Override VNC and Container environment variables
The following VNC environment variables can be overwritten at the `docker run` phase to customize your desktop environment inside the container:
* `VNC_COL_DEPTH`, default: `24`
* `VNC_RESOLUTION`, default: `1920x1080`
* `VNC_PW`, default: `vncpassword`
* `USER`, default: `ros`
* `PASSWD`, default: `ros`

#### 2.1) Example: Override the VNC password
Simply overwrite the value of the environment variable `VNC_PW`. For example in
the docker run command:

    docker run -it -p 5901:5901 -p 6901:6901 -e VNC_PW=vncpassword henry2423/ros-vnc-ubuntu:kinetic

#### 2.2) Example: Override the VNC resolution
Simply overwrite the value of the environment variable `VNC_RESOLUTION`. For example in
the docker run command:

    docker run -it -p 5901:5901 -p 6901:6901 -e VNC_RESOLUTION=800x600 henry2423/ros-vnc-ubuntu:kinetic

### 3) Mounting local directory to conatiner
You should run with following environment variable in order to mapping host user/group with container, and retrieve R/W permission of mounting directory in container (Note: after running this command, the user account in container will be same as host account):

      docker run -it -p 5901:5901 \
        --user $(id -u):$(id -g) \
        --volume /etc/passwd:/etc/passwd \
        --volume /etc/group:/etc/group \
        --volume /etc/shadow:/etc/shadow \
        --volume /home/ros/Desktop:/home/ros/Desktop:rw \
        henry2423/ros-vnc-ubuntu:kinetic


## Installed Robots

### Fixed Manipulators
* Kinova's Jaco, Jaco2, and Micro arms
* Universal Robots (UR3, UR5, UR10)
* PR2

### Wheeled Mobile Robots
* Turtlebot3
* Husky 
* Husarion Rosbot 2.0
* Neobotix differential drive robots (MP-400 and MP-500)
* Neobotix omnidirectional robot with Mecanum wheels (MPO-500)
* Neobotix omnidirectional robot with Omni-Drive-Modules (MPO-700)

### Wheeled Mobile Manipulators
* MM-400: Neobotix mobile platform MP-400 with a robot arm from PILZ, Schunk or Panda 
* MMO-500: Neobotix mobile platform MPO-500 with a robot arm from Universal Robots, Kuka, Rethink Robotics or Schunk
* MMO-700: Neobotix mobile platform MPO-700 with a robot arm from Universal Robots, Kuka, Rethink Robotics or Schunk

### Aerial robots
* RotorS: A MAV gazebo simulator. It provides some multirotor models such as the AscTec Hummingbird, the AscTec Pelican, or the AscTec Firefly, and more.


## Contributors

* [henry2423/docker-ros-vnc](https://github.com/henry2423/docker-ros-vnc) - developed the base Dockerfile used for this image
* [ConSol/docker-headless-vnc-container](https://github.com/ConSol/docker-headless-vnc-container) - developed the ConSol/docker-headless-vnc-container
