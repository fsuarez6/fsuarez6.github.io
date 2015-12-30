---
layout: post
category: projects
title: "Geomagic Touch in ROS"
excerpt: "Worked to add the Geomagic Touch (formerly Sensable PHANToM Omni) haptic device into ROS. Created robot description, tweaked meshes and physical properties."
tags: [C++, PHANToM Omni, Geomagic Touch, Haptics, ROS]
comments: true
---
{% include _toc.html %}

{:refdef: style="text-align: center;"}
<figure>
  <img src="https://raw.github.com/fsuarez6/phantom_omni/hydro-devel/omni_description/resources/OmniRviz.png">
  <figcaption>Omni in RViz</figcaption>
</figure>
{: refdef}

These ROS packages are for the Geomagic Touch (formerly Sensable PHANToM Omni) haptic device -- **firewire** version.

I developed them during my time at the [Group of Robots and Intelligent Machines](http://www.romin.upm.es/), Universidad Politécnica de Madrid.

## Installation

This instructions have been tested with OpenHaptics SDK 3.0 in Ubuntu 12.04, both 32 and 64 bits.
Before going any further please check the following: 

  * You have a **firewire** device NOT the Ethernet one.
  * Your development machine has a **firewire** port. I have tested both Firewire400 and Firewire800 and they work just fine.
  * You understand what a *catkin workspace*, a *symbolic link* and a *git repository* are.

### Requirements
The following are the requirements so that you can run `PHANToMConfiguration`: 
{% highlight bash %}
sudo apt-get install --no-install-recommends freeglut3-dev g++ libdrm-dev libexpat1-dev libglw1-mesa-devlibmotif-dev libncurses5-dev libraw1394-dev libx11-dev libxdamage-dev libxext-dev libxt-dev libxxf86vm-dev tcsh unzip x11proto-dri2-dev x11proto-gl-dev x11proto-print-dev
{% endhighlight %}

### OpenHaptics SDK
In order to connect to the device, you will need to install [OpenHaptics SDK](http://www.dentsable.com/products-openhaptics-toolkit.htm).

### Symbolic Links
Because OpenHaptics SDK puts the libraries in some weird locations you need to create the following symbolic links: (**Only required in 64 bits systems**)
{% highlight bash %}
sudo ln -s /usr/lib/x86_64-linux-gnu/libraw1394.so.11.0.1 /usr/lib/libraw1394.so.8
sudo ln -s /usr/lib64/libPHANToMIO.so.4.3 /usr/lib/libPHANToMIO.so.4
sudo ln -s /usr/lib64/libHD.so.3.0.0 /usr/lib/libHD.so.3.0
sudo ln -s /usr/lib64/libHL.so.3.0.0 /usr/lib/libHL.so.3.0
{% endhighlight %}

### ROS Metapackage

Go to your ROS working directory. e.g.

{% highlight bash %}
cd ~/catkin_ws/src
{% endhighlight %}

Use the `wstool` to install the repository

{% highlight bash %}
wstool init .
wstool merge https://raw.github.com/fsuarez6/phantom_omni/hydro-devel/phantom_omni.rosinstall
wstool update
{% endhighlight %}

Check for any missing dependencies using rosdep:

{% highlight bash %}
rosdep update
rosdep check --from-paths . --ignore-src --rosdistro hydro
{% endhighlight %}


After installing the missing dependencies compile your ROS workspace. e.g.
{% highlight bash %}
cd ~/catkin_ws && catkin_make
{% endhighlight %}

### Testing the Installation

Be sure to always source the appropriate ROS setup file, which for Hydro is done like so:

{% highlight bash %}
source /opt/ros/hydro/setup.bash
{% endhighlight %}

You might want to add that line to your `~/.bashrc`.
Try the `omni.launch` file in the `omni_common` package:

{% highlight bash %}
rosrun omni_common initialize_device.sh -c
roslaunch omni_common omni.launch
{% endhighlight %}
