---
layout: post
title:  "Build Emacs on Ubuntu 16.04"
date:   2016-11-20 23:34:58 -0300
categories: emacs ubuntu
---
This is a set of instructions for compiling the latest Emacs in Ubuntu 16.04. These instructions were mainly taken from [here][ubuntuhandbook].

First of all, make sure you actually want to have the latest Emacs installed. In many cases, the repository version will be just fine.

First, make sure you have the `source` repos enabled. Edit ```/etc/apt/sources.list``` and make sure the lines like this:

```
deb-src http://archive.ubuntu.com/ubuntu/ xenial main restricted
```

are uncommented. Then run ```sudo apt update``` and ```sudo apt-get build-dep emacs24```. This will install all the required dependencies to compile Emacs. There are, well, lots of them.

Now, download Emacs from [here](https://www.gnu.org/software/emacs/download.html) and unpack it.

Inside the directory, do the usual dance:
{% highlight shell %}
./configure
make
sudo make install
{% endhighlight %}

That should do it.

[ubuntuhandbook]: http://ubuntuhandbook.org/index.php/2016/09/install-gnu-emacs-25-1-in-ubuntu-16-04/
