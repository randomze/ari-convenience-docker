# "Easy" Docker Containers for the ARI

This repository contains some helper commands to make getting the ARI development containers up-and-running a bit easier.
It is targeted at Ubuntu and Bash (if you're using Ubuntu and don't know whether you're running bash, you almost surely are).

## Prerequisites

Since PAL provides Docker images for the ARI, you need to install Docker. Follow the guide you can find [here](https://docs.docker.com/engine/install/).
Access to the PAL Docker images requires a Gitlab account which has access to their registry. Contact the ARI responsibles for this.

### Using the GPU

If you have an Nvidia GPU, which is recommendable, you also need to install the Nvidia Container Toolkit. Follow the guide [here](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html).

## Setup

The commands are made available by sourcing the `environment` file. You can either do this manually every time you want to use it, by running `source ./environment`, or add it to your `.bashrc`:

```bash
echo "source $(pwd)/environment" >> ~/.bashrc
```

## Containers

PAL provides a custom image for the ARI, called `gallium`, that is a customized version of `noetic`. Furthermore, several "normal" ROS images can also be used.
Whenever a command calls for an image, the following options are allowed:

- `gallium`
- `noetic`
- `melodic`
- `kinetic`
- `lunar`

The containers mount a source folder, that should be set to what should be contained in the ROS workspace, to the directory `~/catkin_ws/src` in the container.
They also mountain a bridge folder, used to transfer data in/out of the container, in the `~/bridge` directory in the container.

These folder mounts are synchronized with the folders you point them towards on your own computer.
This is quite useful for development, but keep in mind that any change to those folders on the container will be mirrored on the original folders, i.e., deleting things is permanent.

### Creating containers

A container is created through the command:

```bash
make_container <image> <name> <src_folder> <bridge_folder>

# example command
make_container gallium gallium-container ~/src ~/bridge
```

The `src_folder` and `bridge_folder` must point to existing folders.
Containers must be started after being created.

### Starting and stopping containers

Containers are started and stopped by their name:

```bash
start_container <name>
stop_container <name>

# Examples
start_container gallium-container
stop_container gallium-container
```

### Using the containers

There are two ways to use the containers: interactively and running a command.

To get a terminal shell in the container, run:

```bash
open_terminal <name>

# Example
open_terminal gallium-container
```

To simply run a command on the container, without attaching to a shell, run:

```bash
run_command <name> <command>

# Example
run_command gallium-container echo "Hello World"
```

## ROS Master or Slave

The containers have some convenience ROS commands.
Once inside the container, you can easily change between being a ROS Master and a ROS Slave. To make the terminal you're in behave as if the ROS Master is running in your computer, run:

```bash
ros_master
```

If you want the terminal you're in to behave as a slave to some ROS Master, run:

```bash
ros_slave <ROS_MASTER_IP>
```

## What's missing?

Tons of things probably.
Graphics only work with X11 and if you have Wayland the commands break.

