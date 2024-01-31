# "Easy" Docker Containers

This repository contains a script to help make it easier creating dockerized environments that come preloaded with ROS and some other niceties so that we get to the fun part ASAP.

Since I don't know what we'll need it supports multiple versions of ROS:
- kinetic
- lunar
- melodic
- noetic

Also, the images aren't stable yet so if you're going to be running hard to setup environments, with things like Cuda/Torch/etc., come talk to me first.

This is all aimed at Linux. Probably Ubuntu, since that's what I tested[^*].

[^*]: I haven't been able to test that graphics forwarding, like Rviz works yet.

## How to use this

Clone the repository:
```
git clone https://github.com/randomze/hackathon-docker.git
```

Then you can add the environment script to your `.bashrc` so that the commands are always available as soon as open a terminal by doing:
```
cd hackathon-docker
echo "source $(pwd)/environment" >> ~/.bashrc
```

Close & open a new terminal. The main idea now is that you can create one container for each version of ROS. Each containers will mount one of your folders to the `src` folder of a catkin workspace and another of your folders to a folder I call `bridge` so that you can move files in and out of the container easily.

I suggest you try this out by running (I'm using noetic here, but just change it for the other ones if that's what you need):
```
cd ~
mkdir -p noetic-container/src noetic-container/bridge
make_container noetic ~/noetic-container/src ~/noetic-container/bridge
start_container noetic
open_terminal noetic
```

Now you'll be in a terminal inside your noetic container. If you need more terminals, just open more terminals and run `open_terminal noetic`. Anything you do to the `catkin_ws/src` and `bridge` folders in the containers is also always available to you in your own folders `noetic-container/src` and `noetic-container/bridge`, so you can run your editor of choice outside the Docker and just use the terminals to compile things.

If you want to connect your editor to the container for something like debugging, I can vouch for VSCode for Python. Guide for setup is [here](https://code.visualstudio.com/docs/devcontainers/tutorial).

To stop a container, run:
```
close_container noetic
```

If you've irreparably damaged your container by installing/uninstalling things you don't really want, you can delete it and create a new one:
```
delete_container noetic
make_container noetic ~/noetic-container/src ~/noetic-container/bridge
```

**WARNING**: Only the folders `catkin_ws/src` and `bridge` are safe, since mounted outside the container. Anything else that is not in these folders is lost by deleting the container, so be careful when doing this that you've saved everything you don't want to lose in one of these folders.

## What's in the container

ROS and little else. I made sure there's also a text editor (nano/vim) and git. Things you should install yourself via `apt`. If you run a sudo command, the password is `passwd`. If you find yourself in the root user for some reason, the password is `root`.

## What's missing?

I still haven't added the niceties to be able to quickly switch between a ROS Master and ROS Slave setup. Once I've set up the static IP's of the ROS Masters I will update the Docker images. However, this means making new containers, so **if you have annoying to setup things, like CUDA/Torch/etc.** talk to me first please.