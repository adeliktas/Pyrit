# GPU Acceleration #

By far, this is the most awesome feature of Pyrit, besides its storage/speed tradeoff. As of version 0.5.0, to enable GPU computing, you must edit your config file located at `~/.pyrit/config` and set either 
CUDA or OpenCl to 'true' like so:

```
use_CUDA = false
use_OpenCL = true
```
Another cool feature added to the config file is that if `limit_ncpus` is set to a number below zero, Pyrit will disable all CPU cores, piping the work to the GPU, which is usually more (power and performance) efficient.

## Nvidia-CUDA ##

Get yourself a copy of the CUDA-**Toolkit** from http://www.nvidia.com/object/cuda_get.html. You need to modify either _$PATH_ and _ldconfig_ or setup.py if you choose not to install the Toolkit into either '/usr/local/cuda' or '/opt/cuda' so CPyrit-CUDA's installation routine can find Nvidia's compiler 'nvcc'. You also need to have Nvidia's proprietary hardware-drivers installed in the way that fits your OS.

Switch to the directory holding CPyrit-CUDA's source-code inside Pyrit's repository and compile it just like you did with Pyrit:

```
cd modules/cpyrit_cuda/
python setup.py build
sudo python setup.py install
```

Executing _'pyrit list\_cores'_ should list your GPUs. Please see [the troubleshooting-wiki](https://github.com/JPaulMora/Pyrit/wiki/Troubleshooting) if it doesn't work.

**_Note:_** If you are using anacona python you may need to link CUDA libraries to the anaconda /lib directory by doing 

`ln /usr/local/cuda/lib/* /Users/$USER/anaconda/lib/`

## OpenCL ##

OpenCL is currently supported by Nvidia (_GeForce_ GPUs), AMD (_ATI Radeon_ GPUs and SSE3-capable CPUs) and IBM (_CELL B.E._ CPUs). You can get a copy of the SDKs that are required to build CPyrit-OpenCL from the following sites (registration required):

  * [Nvidia OpenCL SDK/Toolkit](http://developer.nvidia.com/object/opencl-download.html)
  * [AMD APP (formerly ATI Stream SDK)](http://developer.amd.com/tools-and-sdks/opencl-zone/amd-accelerated-parallel-processing-app-sdk/)


Please see the drivers' installation instruction for how to get everything up and running. The SDKs usually include simple demos and examples. First try to get those demos working and you'll most probably have no problems installing CPyrit-OpenCL.

Switch to the directory holding CPyrit-OpenCL's source-code inside Pyrit's repository and compile it just like you did with Pyrit:
```
cd modules/cpyrit_opencl/
python setup.py build
sudo python setup.py install
```

Executing _'pyrit list\_cores'_ should list your GPUs. Please see [the Troubleshooting page](https://github.com/JPaulMora/Pyrit/wiki/Troubleshooting) if it doesn't work.