---
layout: post
category: blog
title: "Installing OpenRAVE in Ubuntu 14.04 (Trusty)"
excerpt: "Installation instructions to get OpenRAVE in Ubuntu 14.04"
tags: [Ubuntu, Robotics, OpenRAVE]
comments: true
---
**Last update:** May 23rd, 2017

These instructions are for installing OpenRAVE in Ubuntu 14.04 (Trusty). For the official 
documentation of OpenRAVE, please refer to http://openrave.org.

From my experience, the best way to install OpenRAVE is to build it from source.

## Dependencies

You need to install the following programs:
{% highlight bash %}
sudo apt-get install cmake g++ git ipython octave python-dev python-h5py python-numpy \
python-pip python-scipy python-sympy qt4-dev-tools zlib-bin
{% endhighlight %}

Next, you will need to install the following libraries, which are available from the 
Ubuntu package repository:
{% highlight bash %}
sudo apt-get install libassimp-dev libavcodec-dev libavformat-dev libavformat-dev     \
libboost-all-dev libboost-date-time-dev libbullet-dev libfaac-dev libfreetype6-dev    \
libglew-dev  libgsm1-dev liblapack-dev libmpfi-dev libmpfr-dev libode-dev libogg-dev  \
libpcre3-dev libpcrecpp0 libqhull-dev libqt4-dev libsoqt-dev-common libsoqt4-dev      \
libswscale-dev libswscale-dev libvorbis-dev libx264-dev libxml2-dev libxvidcore-dev
{% endhighlight %}

Now, get the `collada-dom-dev` package. If you are installing OpenRAVE is very likely that 
you have already installed ROS Indigo. 
In that case, you can install `collada-dom-dev` directly:
{% highlight bash %}
sudo apt-get update
sudo apt-get install collada-dom-dev
{% endhighlight %}

If you are not planning to use ROS, install `collada-dom-dev` from the OpenRAVE PPA:
{% highlight bash %}
sudo add-apt-repository ppa:openrave/release
sudo sh -c 'echo "deb-src http://ppa.launchpad.net/openrave/release/ubuntu `lsb_release -cs` main" >> /etc/apt/sources.list.d/openrave-release-`lsb_release -cs`.list'
sudo apt-get update
sudo apt-get install collada-dom-dev
{% endhighlight %}

### OpenSceneGraph
OpenRAVE requires OpenSceneGraph 3.4, so we have to build it from source:
{% highlight bash %}
cd ~/git
git clone https://github.com/openscenegraph/OpenSceneGraph.git --branch OpenSceneGraph-3.4
cd OpenSceneGraph
mkdir build; cd build
cmake .. -DDESIRED_QT_VERSION=4
make -j `nproc`
sudo make install
{% endhighlight %}

### FCL - The Flexible Collision Library
Install `libccd`:
{% highlight bash %}
sudo apt-add-repository ppa:imnmfotmal/libccd
sudo apt-get update
sudo apt-get install libccd
{% endhighlight %}

Build and install `fcl` version 0.5.0:
{% highlight bash %}
cd ~/git
git clone https://github.com/flexible-collision-library/fcl
cd fcl; git reset --hard 0.5.0
mkdir build; cd build
cmake ..
make -j `nproc`
sudo make install
{% endhighlight %}

## `sympy` Version
Downgrade sympy so that IKFast works properly:
{% highlight bash %}
pip install --upgrade --user sympy==0.7.1
{% endhighlight %}

## Building from Source

Now you can build and install `OpenRAVE` (Tested with commit 81ec501):
{% highlight bash %}
cd ~/git
git clone https://github.com/rdiankov/openrave.git
cd openrave; git reset --hard 81ec501
mkdir build; cd build
cmake -DODE_USE_MULTITHREAD=ON -DOSG_DIR=/usr/local/lib64/ ..
make -j `nproc`
sudo make install
{% endhighlight %}

## Testing the Installation
You can check that your installation works by running one of the 
[default examples](http://openrave.org/docs/latest_stable/examples/). For example:
{% highlight bash %}
openrave.py --example graspplanning
{% endhighlight %}

It should start the `graspplanning` example:
{:refdef: style="text-align: center;"}
<figure>
  <img src="{{ site.url }}/images/graspplanning.png">
  <figcaption>OpenRAVE example</figcaption>
</figure>
{: refdef}

You may see some messages printed in yellow on your command line but they are 
just warnings that won't prevent your installation from working.
