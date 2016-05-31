---
layout: post
category: blog
title: "Robotics Workstation Setup in Ubuntu 16.04 (Xenial)"
excerpt: "Installation instructions to setup your development workstation in Ubuntu 16.04 for robotics"
tags: [Ubuntu, Xenial, Robotics, OpenRAVE]
comments: true
---
These instructions are for setting up your robotics workstation in **Ubuntu 16.04 (Xenial)**.

After this, you will have installed the following tools:

* [ROS Kinetic](http://wiki.ros.org/kinetic)
* [OctoMap](https://octomap.github.io/)
* [FCL - The Flexible Collision Library](https://github.com/flexible-collision-library/fcl)
* [OSG - OpenSceneGraph](http://www.openscenegraph.org/)
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

## Dependencies
You need to install the following programs:
{% highlight bash %}
sudo apt-get install build-essential cmake doxygen g++ git ipython octave python-dev  \
python-h5py python-numpy python-pip python-scipy python-sympy qt4-dev-tools minizip
{% endhighlight %}

Next, you will need to install the following libraries, which are available from the Ubuntu package repositories:
{% highlight bash %}
sudo apt-get install libassimp-dev libavcodec-dev libavformat-dev libboost-all-dev    \
libboost-date-time-dev libbullet-dev libfaac-dev libfreetype6-dev libglew-dev         \
libgsm1-dev liblapack-dev libmpfi-dev libmpfr-dev libode-dev libogg-dev               \
libopenscenegraph-dev libpcre3-dev libqglviewer-dev-qt4 libqhull-dev libqt4-dev       \
libqt4-opengl-dev libsoqt-dev-common libsoqt4-dev libswscale-dev libswscale-dev       \
libvorbis-dev libx264-dev libxml2-dev libxvidcore-dev
{% endhighlight %}

Finally, *if you decide to install OpenSceneGraph*, you will need these libraries:
{% highlight bash %}
sudo apt-get install libavdevice-dev libcairo2-dev libcurl4-gnutls-dev libgdal-dev    \
libgstreamer1.0-dev libgstreamermm-1.0-dev libgstreamer-ocaml-dev                     \
libgstreamer-plugins-bad1.0-dev libgstreamer-plugins-base1.0-dev                      \
libgstreamer-plugins-good1.0-dev libgtk2.0-dev libgtkglext1-dev libjasper-dev         \
libjpeg-dev libpoppler-glib-dev libpthread-workqueue-dev libsdl1.2-dev libsdl2-dev    \
librsvg2-dev libtiff5-dev
{% endhighlight %}

## OSG - OpenSceneGraph

Build and install the GTA library ([http://www.nongnu.org/gta/download.html](http://www.nongnu.org/gta/download.html)):
{% highlight bash %}
cd ~/git
git clone git://git.savannah.nongnu.org/gta.git
cd gta/libgta
autoreconf -i
./configure; make -j `nproc`; sudo make install
cd ../gtatool
autoreconf -i
./configure; make -j `nproc`; sudo make install
{% endhighlight %}

Build and install `OpenSceneGraph` version `3.4.0`:
{% highlight bash %}
cd ~/git
git clone https://github.com/openscenegraph/OpenSceneGraph.git
cd OpenSceneGraph; git checkout OpenSceneGraph-3.4.0
mkdir build; cd build
cmake ..
sudo make install_ld_conf
make -j `nproc`
sudo make install
{% endhighlight %}

## FCL - The Flexible Collision Library

Build and install `octomap` version `1.7.2`:
{% highlight bash %}
cd ~/git
git clone https://github.com/OctoMap/octomap.git
cd octomap; git checkout v1.7.2
mkdir build; cd build
cmake ..
make -j `nproc`
sudo make install
{% endhighlight %}

Build and install `libccd`:
{% highlight bash %}
cd ~/git
git clone https://github.com/danfis/libccd
cd libccd
mkdir build; cd build
cmake ..
make -j `nproc`
sudo make install
{% endhighlight %}

Finally, build and install `fcl`:
{% highlight bash %}
cd ~/git
git https://github.com/flexible-collision-library/fcl
cd fcl
mkdir build; cd build
cmake ..
make -j `nproc`
sudo make install
{% endhighlight %}

## OpenRAVE
Downgrade sympy so that IKFast works properly:
{% highlight bash %}
sudo pip install --upgrade sympy==0.7.1
{% endhighlight %}

Build and install `OpenRAVE`:
{% highlight bash %}
cd ~/git
git clone https://github.com/rdiankov/openrave.git --branch master
cd openrave
mkdir build; cd build
cmake -DODE_USE_MULTITHREAD=ON -DOPT_QTOSG_VIEWER=OFF ..
make -j `nproc`
sudo make install
{% endhighlight %}

## Testing the Installation
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

You may see some messages printed in yellow on your command line but they are just warnings that won't prevent your installation from working.
