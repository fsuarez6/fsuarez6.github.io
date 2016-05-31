---
layout: post
category: blog
title: "Installing OpenRAVE in Ubuntu 14.04 (Trusty)"
excerpt: "Installation instructions to get OpenRAVE in Ubuntu 14.04"
tags: [Ubuntu, Robotics, OpenRAVE]
comments: true
---
These instructions are for installing OpenRAVE in Ubuntu 14.04 (Trusty). For the official documentation of OpenRAVE, please refer to http://openrave.org.

From my experience, the best solution to install OpenRAVE today is to build from source.

## Dependencies

You need to install the following programs:
{% highlight bash %}
sudo apt-get install cmake g++ git ipython octave python-dev python-h5py python-numpy python-pip python-scipy python-sympy qt4-dev-tools zlib-bin
{% endhighlight %}

Downgrade sympy so that IKFast works properly:
{% highlight bash %}
pip install --upgrade --user sympy==0.7.1
{% endhighlight %}

Next, you will need to install the following libraries, which are available from the Ubuntu package repository. (Thanks to [Stephane](https://scaron.info/teaching/installing-openrave-on-ubuntu-14.04.html) for the list):
{% highlight bash %}
sudo apt-get install libassimp-dev libavcodec-dev libavformat-dev libavformat-dev libboost-all-dev libboost-date-time-dev libbullet-dev libfaac-dev libfreetype6-dev libglew-dev  libgsm1-dev liblapack-dev libmpfi-dev libmpfr-dev libode-dev libogg-dev libopenscenegraph-dev libpcre3-dev libpcrecpp0 libqhull-dev libqt4-dev libsoqt-dev-common libsoqt4-dev libswscale-dev libswscale-dev libvorbis-dev libx264-dev libxml2-dev libxvidcore-dev
{% endhighlight %}

Almost there... Now, get the `collada-dom-dev` package. If you are installing OpenRAVE is very likely that you already installed ROS Indigo. In that case, you can install `collada-dom-dev` directly:
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

## Building from Source

Clone the OpenRAVE's `master` from GitHub (tested with commit 677ef40c60ae29b3e584dc883a799d126cc55752):
{% highlight bash %}
cd ~/git
git clone --branch master https://github.com/rdiankov/openrave.git
{% endhighlight %}

Build it with CMake:
{% highlight bash %}
cd openrave
mkdir build
cd build
cmake ..
make
sudo make install
{% endhighlight %}

## Testing the Installation
You can check that your installation works by running one of the [default examples](http://openrave.org/docs/latest_stable/examples/). For example:
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
