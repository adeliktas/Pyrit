# Introduction #

This document will guide you through the installation of Pyrit and it's modules.

Pyrit compiles and runs on Linux, FreeBSD and MacOS. Windows is not (and probably never will be) supported; there are however some reports of successful installations on Windows with the help of [MinGW](http://www.mingw.org/).

Pyrit consists of basically two parts:

  * The main module features the commandline-client, the scheduling- and database-code and a basic extension-module that uses the CPU for computation. The main module is required for everyone...
  * There are currently two extension modules that add support for more advanced hardware. The extension modules for [Nvidia-CUDA](http://www.nvidia.com/object/cuda_home.html) and [OpenCL](http://www.khronos.org/opencl/) may be installed optionally and are used if available and supported by local hardware.

You can choose between OpenCL and CUDA if you have a compatible Nvidia-GPU; you may want to take a look at [this page](http://www.nvidia.com/object/cuda_learn_products.html) to find out if your hardware supports [Nvidia-CUDA](http://www.nvidia.com/object/cuda_home.html).
People with GPUs from ATI are supported through [AMD's OpenCL-implementation](http://developer.amd.com/gpu/AMDAPPSDK/Pages/default.aspx) and may find [this page](http://developer.amd.com/gpu/AMDAPPSDK/pages/DriverCompatibility.aspx) of interest; other possible OpenCL-platforms like [IBM's Cell B.E.](http://www-03.ibm.com/technology/cell/) (that powers the Playstation 3) should work but are untested at the moment.


# Compiling from sources (Releases or Git) #

## General Dependencies ##
Compiling from source-code is the preferred way of getting Pyrit onto your system. Linux users running a binary distribution may need to install the development packages for:
* [Python](http://www.python.org) e.g. _python-devel_ (CentOS/Fedora) or _python-dev_   (Ubuntu/Debian) 
* [OpenSSL](http://www.openssl.org/) e.g. _openssl-devel_ or _libssl-dev_ (you get which one) and 
* [Zlib](http://zlib.net/) (e.g. _zlib-devel_). 

You also need a C-compiler like _gcc_. Users of MacOS probably only need to have [XCode](http://developer.apple.com/TOOLS/Xcode/) installed.

From time to time Pyrit get's packed into (hopefully) stable packages. In general you should download, compile and install these source-code packages from the [Latest Release](https://github.com/JPaulMora/Pyrit/releases).

The more adventurous among you may instead want to try the latest source-code in Pyrit's repository. The code in [The Main page](https://github.com/JPaulMora/Pyrit/) may include more features and provide better performance but also may cause random problems or even not compile at all. Use the fixed packages when in doubt.

### Stable: Source-code from fixed packages ###

Download the source-code package for Pyrit, it includes the (optional) extension modules.

  * Pyrit (required): [Latest Version](https://github.com/JPaulMora/Pyrit/releases)
  * CPyrit-CUDA (optional, for Nvidia-hardware)
  * CPyrit-OpenCL (optional, for compatible hardware)
Now unpack the source-code into a new directory like this:
```
unzip Pyrit-vX.zip
cd Pyrit-X
```
You may use an archive manager for easier unpacking. 

Assuming you have successfully installed dependencies listed above, continue with the compiling as explained below.

### Adventurous: Source-code from git ###

You need to install _git_ before you can use Pyrit's repository; most Linux distributions provide a package for that. Do the initial clone from git like this:
```
git clone https://github.com/JPaulMora/Pyrit.git
```

This will create a new directory called _'Pyrit'_ that holds all of Pyrit's latest source-code. Execute ` git pull ` inside that directory to keep track of changes, you may need to recompile for changes to take effect.

### Compiling and installing ###

#### ... the main module ####

Switch to the main module's directory. We need to install some python dependencies first, for which we will use the `pip` command (you can use [anaconda](http://anaconda.org) too), we use Python's _distutils_ to compile and install the code:

```
sudo pip install psycopg2             ##default, install to use SQL database access.
conda install psycopg2                ##only if you have anaconda python installed.

sudo pip install scapy                ##default, scapy is required for analize/attack functionality.
sudo apt-get install python-scapy     ##If above command didn't work, in debian systems you can try this.

conda install --channel https://conda.anaconda.org/pdrops scapy ##Again, only if you use anaconda.

cd Pyrit                 ## Compile time!
python setup.py clean
python setup.py build
```

If everything went well and no errors are thrown at you, use _distutils_ again to install Pyrit:

```
sudo python setup.py install
```

You can now execute `pyrit` from your commandline; leave the source-code's directory before doing so to prevent Python from getting confused with module-lookups by executing `cd`.

For instructions on how to install CUDA or OpenCL add-ons please refer to [this](https://github.com/JPaulMora/Pyrit/wiki/GPU-Acceleration) wiki page on the subject.

## Using binary packages ##

Binary packages are not directly supported and don't provide the GPU acceleration modules, but provide an easy way of getting Pyrit into your system. Distros like [Pentoo](http://www.pentoo.ch/), [Kali Linux 1/2](https://www.kali.org/) include Pyrit as pre-built packages, finally [Ubuntu 14.04 LTS](http://www.ubuntu.com/) lets you `apt-get install pyrit`.

### **Kali Linux Support Section for NVidia GPU's** ( run lspci | grep VGA , if it says NVIDIA, proceed)

This assumes FULL fresh install of kali on a 64bit system. However you can SKIP down to the updates if you already have a fresh install

`cd ~/Downloads`

`wget http://cdimage.kali.org/kali-2018.2/kali-linux-2018.2-amd64.iso`

#Start with a USB key NOT plugged in

`lsblk`

- Record the /dev/sdX locations

- Plug in USB you plan on using

`lsblk`

- You will notice a change. The new one is your device you'll be using for this next command.

`dd if=~/Downloads/kali-linux-2018.2-amd64.iso of=/dev/sdX`  then press enter. X represents the letter of YOUR device. 

This "burns" the ISO to your device.

It will take a number of minutes. Possibly upwards of 5-15, depending on your device and hardware.

Reboot when done, and select from the boot menu your device, and either select persistence, or install to another drive, and when finished you may begin by opening a terminal window and continuing below

The first thing I like to do is go into Show applications, then Settings, then select the Power tab and turn off suspend after XX minutes. Its rather annoying to run an upgrade or a lengthy process and have your system suspend automatically for you ;) 

`apt-get update -y && apt-get upgrade -y && apt-get dist-upgrade -y`

REBOOT

`apt-get install linux-headers-$(uname -r) -y`

If installing headers fails, you probably didn't dist-upgrade. go back and run the updates again.

`apt-get install nvidia-kernel-dkms -y`

You'll possibly be prompted to answer some things. Just say yes or OK to everything. 

`apt-get install nvidia-xconfig -y && nvidia-xconfig`

REBOOT

`apt-get install mesa-utils -y && glxinfo | grep -i "direct rendering"`

should say yes. 

`lsmod | grep nvidia `

make sure it says a bunch of nvidia stuff so you know its loaded

**IMPORTANT**

`lsmod | grep nouveau`

this MUST return nothing. blank. 

`apt-get install nvidia-cuda-toolkit nvidia-opencl-icd`

`apt-get install libssl-dev libz-dev libpcap-dev`

This Assumes you are using ~/Downloads as your area for working.

`cd ~/Downloads/ && git clone https://github.com/JPaulMora/Pyrit.git && cd Pyrit && python setup.py build`

should return NO errors, so run:

`gedit ~/Downloads/Pyrit/modules/cpyrit_opencl/_cpyrit_opencl.c`

Scroll down to the #includes section and above the other include entries put these two lines

> #define CL_USE_DEPRECATED_OPENCL_1_2_APIS

> #include <CL/cl.h>

save file and quit

The reason for this is clCreateCommandQueue is depreciated OpenCL2.0 and above and you must do this to successfully build or you will error out. 

`cd ~/Downloads/Pyrit/modules/cpyrit_opencl`

`python setup.py build`

No errors ?

`python setup.py install`

Ok great, now do this

`gedit ~/.pyrit/config`

find use_OpenCL = false and change it to true. thats it save and exit

`pyrit list_cores`

you should see your device(s)  

`pyrit benchmark`

if no errors you are done. 
Enjoy!

If you DO get errors, AND you happen to have multiple GPU's installed, try running hashcat -b -m 2500 -d 1
Or -d 2. If either of those works, then you'll have to figure that one out, I removed my card, as I don't have sufficient power to run both anyways.
Good luck! 




