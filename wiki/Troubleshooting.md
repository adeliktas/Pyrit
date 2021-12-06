### OSX/macOS libcrypto not being found ###

The error looks something like this:
````
ImportError:(/path/to/_cpyrit_cpu.so,2): Library not loaded: libcrypto.1.0.0.dylib
  Referenced from: /path/to/_cpyrit_cpu.so
  Reason: image not found
````
If thats the case for you, thats because Apple dropped OpenSSL since OS X 10.7 and replaced it with their own libraries, to fix this you need to run the commands below (I assume you already installed brew):

```
brew install openssl                                                        #Downloads the required files.
sudo ln /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib     #Puts the files in place.
```

Alternatively, you can set `LDFLAGS` and `CPPFLAGS` as recommended in the Homebrew openssl documentation (`brew info openssl`):

```shell_session
$ LDFLAGS=-L/usr/local/opt/openssl/lib CPPFLAGS=-I/usr/local/opt/openssl/include pip install .
```

### GPU performance is reduced on system with Hyper Threading ###

NVidia/ATI GPU driver requires at least one real core per GPU for efficient work. With HT enabled, the driver needs to fight for CPU cycles with CPU computing core. This leads to GPU starvation and decreased performance. The problem can be solved by reducing number of running CPU-cores: Open '.pyrit/config' and set 'limit\_ncpus' to the number of physical CPU-cores.

### The GPU does not show up in '_list\_cores_' ###

Pyrit suppresses most errors that occur while loading the GPU-extensions as they are usually caused by simply not having compatible hardware installed. Open a terminal and try loading the offending module directly to get more information.

For CPyrit-OpenCL:

```
python -c 'from cpyrit import _cpyrit_opencl'
```

For CPyrit-CUDA:

```
python -c 'from cpyrit import _cpyrit_cuda'
```

There is no output at all if the module was loaded successfully. Some of the errors you might get include:

  * **`ImportError: cannot import name _cpyrit_cuda / ImportError: cannot import name _cpyrit_opencl`**

> The CPyrit-OpenCL or CPyrit-CUDA modules are simply not installed or can't be found in the current Python-environment. Try (re-) installing the extension modules and make sure you don't mix the Python-interpreters you use (this can happen on MacOS which tends to have multiple versions of Python installed).

  * **`ImportError: libcuda.so.1: cannot open shared object file: No such file or directory`**

> The CPyrit-CUDA modules requires Nvidia's proprietary driver to be installed. Depending on your distribution you may also need to symlink e.g. '/usr/lib/nvidia/libcuda.so.1' to '/usr/lib/libcuda.so.1' or add '/usr/lib/nvidia' to your _ldconfig_.

  * **`NVIDIA: could not open the device file /dev/nvidiactl (No such file or directory).`**

> Ensure that the 'nvidia' module has been successfully loaded. Taking a look at the kernel ringbuffer may give more help:

```
 modprobe nvidia
 dmesg | tail
```

  * **`ImportError: CUDA seems to be unavailable or no device reported.`**

> The CUDA-driver has loaded but failed to initialize or reports that none of your GPUs is compatible. Take a look at [this page](http://www.nvidia.com/object/cuda_learn_products.html) to find out if your GPU is supported. You may need to update your drivers.

### Xorg/Screen becomes slow and unresponsive when using Pyrit on the GPU ###

O rly ?

### Pyrit says it stored only Y passwords after trying to import a wordlist of X entries ###

Pyrit ignores passwords that have less than 8 or more than 63 characters as those can't be used for WPA/WPA2 encryption anyway.

### The .pyrit directory has a size of only Y MBs after importing a wordlist of X GBs ###

Pyrit uses [zlib](http://www.zlib.net)-compression to store the passwords when using file:// storage. Database storage may vary.


### Getting errors after setting up database ###

SQLalchemy is not installed:
```
Connecting to storage at 'sqlite:///mydb.db'...  Traceback (most recent call 
last):
  File "/usr/local/bin/pyrit", line 6, in <module>
    pyrit_cli.Pyrit_CLI().initFromArgv()
  File "/usr/local/lib/python2.7/dist-packages/pyrit_cli.py", line 116, in initFromArgv
    options['storage'] = self._getStorage(storage_url)
  File "/usr/local/lib/python2.7/dist-packages/pyrit_cli.py", line 231, in _getStorage
    storage = cpyrit.storage.getStorage(url)
  File "/usr/local/lib/python2.7/dist-packages/cpyrit/storage.py", line 132, in getStorage
    raise util.SqlalchemyImportError("SQLAlchemy seems to be " \
cpyrit.util.SqlalchemyImportError: SQLAlchemy seems to be unavailable.
```

### Scapy 2.x is required to use Pyrit's analyze/attack functions but seems to be unavailable. ###

This error happens because you forgot to install scapy, read instructions below.

If you are using stock Python, you can simply run:
OSX:
`pip install scapy` 

Linux:
same as above or `sudo apt-get install python-scapy`

Anaconda:
`conda install --channel https://conda.anaconda.org/pdrops scapy`

### There is another problem with Pyrit ###
Submit an [Issue](https://github.com/JPaulMora/Pyrit/issues).