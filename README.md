[//]: # Docker-Blimp
[//]: # ============

***A docker image to simulate lighter than air vehicles (LTAVs) in ROS/Gazebo***

**Author:** *Wail Gueaieb*


<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**

- [Overview](#overview)
    - [ROS/Gazebo Installation](#rosgazebo-installation)
    - [Airship Simulation Installation](#airship-simulation-installation)
- [Getting the Image](#getting-the-image)
    - [Pulling the Image from Docker Hub](#pulling-the-image-from-docker-hub)
    - [Building the Image Locally](#building-the-image-locally)
- [Running the Container](#running-the-container)
    - [Connect & Control](#connect--control)
    - [Environment Settings](#environment-settings)
        - [Using root (user id `0`)](#using-root-user-id-0)
        - [Using user and group id of host system](#using-user-and-group-id-of-host-system)
        - [Override VNC and container environment variables](#override-vnc-and-container-environment-variables)
            - [Example: Override the VNC password](#example-override-the-vnc-password)
            - [Example: Override the VNC resolution](#example-override-the-vnc-resolution)
        - [Mounting local directory to conatiner](#mounting-local-directory-to-conatiner)

<!-- markdown-toc end -->



# Overview
This is a docker image aimed to help researchers and hobbyists to experiment with and simulate lighter than air vehicles (LTAVs) in ROS/Gazebo.

The image is composed of two main parts: the first part pertains to the installation of ROS/Gazebo, while the second pertains to the installation of the airship simulation, as described below.

## ROS/Gazebo Installation
This part is based on the repository from henry2423/docker-ros-vnc: [https://github.com/henry2423/docker-ros-vnc](https://github.com/henry2423/docker-ros-vnc). Most of the documentation for that repository is valid for this one, except:
  * Only ROS Melodic is supported here (ROS Kinetic and Lunar are not).
  * Tensorflow and Jupyter are not installed.
  * ROS-melodic-desktop-full is used instead of the original version.

## Airship Simulation Installation
This part comes from the respository airship_simulation on Github [https://github.com/robot-perception-group/airship_simulation](https://github.com/robot-perception-group/airship_simulation).

# Getting the Image
The image can be either pulled directly from Docker Hub, or built locally your personal computer. The former method may be much more convenient. 

## Pulling the Image from Docker Hub
Currently, the image lives at a Docker Hub repository [https://hub.docker.com/r/realjsk/docker-blimp](https://hub.docker.com/r/realjsk/docker-blimp "realjsk/docker-blimp"). It can be pulled using the docker command:  
`docker pull realjsk/docker-blimp:20210323` 

* `henry2423/ros-vnc-ubuntu:melodic`: __Ubuntu 18.04 with `ROS Melodic + Gazebo 9`__

  [![](https://images.microbadger.com/badges/version/henry2423/ros-vnc-ubuntu:melodic.svg)](https://hub.docker.com/r/henry2423/ros-vnc-ubuntu/) [![](https://images.microbadger.com/badges/image/henry2423/ros-vnc-ubuntu:melodic.svg)](https://microbadger.com/images/henry2423/ros-vnc-ubuntu:melodic)

* `realjsk/docker-blimp:20210323`: __Ubuntu 18.04 with `ROS Melodic + Gazebo 9`__

  ![GitHub all releases](https://img.shields.io/github/downloads/atom/atom/total)
  ![Docker Image Size (tag)](https://img.shields.io/docker/image-size/henry2423/ros-vnc-ubuntu/melodic)
  ![Docker Image Version (tag latest semver)](https://img.shields.io/docker/v/henry2423/ros-vnc-ubuntu/melodic)

## Building the Image Locally
The source files to build the image on a local computer are stored at the Github repository [https://github.com/wail-uottawa/Docker-Blimp](https://github.com/wail-uottawa/Docker-Blimp)

1. Start by cloning the repository:  
   `git clone https://github.com/wail-uottawa/Docker-Blimp.git`
2. Then, cd to directory including the file `Dockerfile` and (with the docker server running) build the image:  
   `docker build -t realjsk/blimp:20210323  .` (note the dot at the end)  
   If you want, you may change `realjsk/blimp` and `20210323` to any other image name and image tag of your choice, respectively.  
   This can also be done by running the shell script `docker-build.sh` using the command:  
   `sh docker-build.sh`  
   Feel free to edit the shell script if you want.

# Running the Container
The container is developed under xfce-docker-container source, which makes it accessible through xfce-vnc or no-vnc (via http vnc service). In the following, it is assumed that the `name:tag` of the Docker image is the same as above (`realjsk/blimp:20210323`). If you used a different one, please make the necessary changes to the proceeding command.

- Run command with mapping to local port `5901` (vnc protocol) and `6901` (vnc web access):

      `docker run -d -p 5901:5901 -p 6901:6901 realjsk/blimp:20210323`

- If you want to get into the container use interactive mode `-it` and `bash`
      
      `docker run -it -p 5901:5901 -p 6901:6901 realjsk/blimp:20210323 bash`

## Connect & Control
Once it is run, you can connect to the container in a number of ways:
* connect via __VNC viewer `localhost:5901`__, default password: `vncpassword`
* connect via __noVNC HTML5 full client__: [`http://localhost:6901/vnc.html`](http://localhost:6901/vnc.html), default password: `vncpassword` 
* connect via __noVNC HTML5 lite client__: [`http://localhost:6901/?password=vncpassword`](http://localhost:6901/?password=vncpassword) 

The default username and password in container is ros:ros

## Environment Settings

### Using root (user id `0`)
Add the `--user` flag to your docker run command. For example:

	docker run -it --user root -p 5901:5901 realjsk/blimp:20210323 bash

### Using user and group id of host system
In Unix-like host systems, you may add the `--user` flag to your docker run command. For example:

    docker run -it -p 5901:5901 --user $(id -u):$(id -g) realjsk/blimp:20210323

Note: the uid and gui of the host system may not be able to map to those of the container, which is 1000:1000. In that case, you may want to try overriding the VNC and container envirenment variables, as explained in below (see [Override VNC and container environment variables](#override-vnc-and-container-environment-variables)).

### Override VNC and container environment variables
The following VNC environment variables can be overwritten within the Docker running command to customize the desktop environment inside the container:
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

### Mounting local directory to container
Docker enables the mapping between directories on the host system and the container through the `volume` directive. For example, the following command maps the host user/group with the container, and also maps a few directories between the host and the container. Note that in such cases, the host serves as the master while the container is the slave. With the following command, for instance, the user account in the container will be same as the host account.

      docker run -it -p 5901:5901 \
        --user $(id -u):$(id -g) \
        --volume /etc/passwd:/etc/passwd \
        --volume /etc/group:/etc/group \
        --volume /etc/shadow:/etc/shadow \
        --volume /home/ros/Desktop:/home/ros/Desktop:rw \
        realjsk/blimp:20210323

