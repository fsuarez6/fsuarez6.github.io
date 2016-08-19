---
layout: post
category: blog
title: "Robotics Workstation Setup in Ubuntu 16.04 (Xenial)"
excerpt: "Installation instructions to setup your development workstation in Ubuntu 16.04 for robotics"
tags: [Ubuntu, Xenial, Robotics, OpenRAVE]
comments: true
---
**Last update:** August 18, 2016

These instructions are for setting up your robotics workstation in **Ubuntu 16.04 (Xenial)**.

After this, you will have installed the following tools:

* [ROS Kinetic](http://wiki.ros.org/kinetic)
* [OctoMap](https://octomap.github.io/)
* [FCL - The Flexible Collision Library](https://github.com/flexible-collision-library/fcl)
* [OpenRAVE](http://openrave.org)

For the official documentation, please refer to the website of the corresponding tool.

## ROS Kinetic
For up-to-date instructions to install ROS Kinetic, please refer to: [http://wiki.ros.org/kinetic/Installation/Ubuntu](http://wiki.ros.org/kinetic/Installation/Ubuntu)
{% highlight bash %}
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main"      \
> /etc/apt/sources.list.d/ros-latest.list'
sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net:80 --recv-key 0xB01FA116
sudo apt-get update
sudo apt-get install ros-kinetic-ros-base rviz
{% endhighlight %}

### Initialize rosdep
Before you can use ROS, you will need to initialize rosdep. rosdep enables you to easily install system dependencies for source you want to compile and is required to run some core components in ROS.
{% highlight bash %}
sudo rosdep init
rosdep update
{% endhighlight %}

### Create a ROS Workspace
Let's create a [catkin workspace](http://wiki.ros.org/catkin/workspaces):

{% highlight bash %}
source /opt/ros/kinetic/setup.bash
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/src
catkin_init_workspace
{% endhighlight %}

Even though the workspace is empty (there are no packages in the `src` folder, just a single `CMakeLists.txt` link) you can still *build* the workspace:

{% highlight bash %}
cd ~/catkin_ws/
catkin_make
{% endhighlight %}

Before continuing, source your new `setup.bash` file:

{% highlight bash %}
source ~/catkin_ws/devel/setup.bash
{% endhighlight %}

To make sure your workspace is properly overlayed by the setup script, make sure `ROS_PACKAGE_PATH` environment variable includes the directory you're in:

{% highlight bash %}
$ echo $ROS_PACKAGE_PATH
/home/$USER/catkin_ws/src:/opt/ros/kinetic/share:/opt/ros/kinetic/stacks
{% endhighlight %}

## OpenRAVE
At this point OpenRAVE has a considerable number of dependencies that have to be installed manually. For the sake of simplicity, this instructions will show you how to get working OpenRAVE 0.9.0 with enought features.

### Dependencies

You need to install the following programs:
{% highlight bash %}
sudo apt-get install build-essential cmake doxygen g++ git ipython octave python-dev  \
python-h5py python-numpy python-pip python-scipy python-sympy qt5-default minizip
{% endhighlight %}

Next, you will need to install the following libraries, which are available from the Ubuntu package repositories:
{% highlight bash %}
sudo apt-get install ann-tools cmake libann-dev libassimp-dev libavcodec-dev          \
libavformat-dev libboost-python-dev libboost-all-dev libccd-dev                       \
libcollada-dom2.4-dp-dev libeigen3-dev libflann-dev liblapack-dev liblog4cxx-dev      \
libminizip-dev liboctave-dev libode-dev libpcre3-dev libqhull-dev libsoqt-dev-common  \
libsoqt4-dev libswscale-dev libtinyxml-dev libxml2-dev octomap-tools
{% endhighlight %}

### FCL - The Flexible Collision Library
Build and install `fcl` version 0.5.0:
{% highlight bash %}
cd ~/git
git clone https://github.com/flexible-collision-library/fcl
cd fcl; git checkout 0.5.0
mkdir build; cd build
cmake ..
make -j `nproc`
sudo make install
{% endhighlight %}


### `sympy` Version
Downgrade sympy so that IKFast works properly:
{% highlight bash %}
pip install --upgrade --user sympy==0.7.1
{% endhighlight %}

### Build and Install
Now you can build and install `OpenRAVE` (Tested with commit 30baaa66e1f43091220ce56f67047d29f07b8801):
{% highlight bash %}
cd ~/git
git clone https://github.com/rdiankov/openrave.git --branch master
cd openrave
mkdir build; cd build
cmake -DODE_USE_MULTITHREAD=ON -DOPENRAVE_PLUGIN_MOBYRAVE=OFF -DOPT_QTOSG_VIEWER=OFF ..
make -j `nproc`
sudo make install
{% endhighlight %}

### Testing the Installation
You can check that your installation works by running one of the [default examples](http://openrave.org/docs/latest_stable/examples/):
{% highlight bash %}
openrave.py --example graspplanning
{% endhighlight %}

It should start the `graspplanning` example:
{:refdef: style="text-align: center;"}
<figure>
  <img src="{{ site.url }}/images/graspplanning.jpg">
  <figcaption>OpenRAVE example</figcaption>
</figure>
{: refdef}

You may see some messages printed in yellow on your terminal but they are just warnings that won't prevent your installation from working.
