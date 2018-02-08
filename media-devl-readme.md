# Intel(R) C for Media Development Package


## Introduction

The Intel(R) C for Media Development Package is a software development package for the Intel(R) graphics hardware architecture. 

## License

The Intel(R) C for Media Development Package is distributed under the MIT.
You may obtain a copy of the License at:
https://opensource.org/licenses/MIT

## Prerequisites

- Ubuntu 16.04 with Linux kernel 4.10 (For kernel version other than 4.10, the full functionality can't be guaranteed.)
- make
- g++

## Dependencies (Intel Components)

- Intel(R) Media Driver for VAAPI
- Intel(R) C for Media Runtime ( included in Media Driver)
- Intel(R) C for Media Compiler
- Intel(R) Graphics Compiler

## Install

### Install the Intel(R) C for Media Development Package

#### Folder structure after installation
```
<package_root>
            ├── compiler
            │   ├── bin
            │   └── include_llvm
            ├── documents
            │   ├── cmrtlib
            │   └── compiler
            ├── drivers
            │   ├── media_driver
            │   │   ├── release
            │   │   └── release-internal
            │   └── IGC
            ├── examples
            ├── readme.txt
            └── runtime
                ├── include
                └── lib
```

#### Details of folder structure

- **compiler**: the compiler binaries and libraries used to build CM language source codes to GPU kernels running on Render engine

- **compiler/bin**: The front-end and offline back-end compiler binaries to build GPU kernels
    
- **compiler/include_llvm**: The header files needed during compiling GPU kernels
    
- **runtime**: the libraries and headers defines a set of CM APIs used by CPU applications, through which GPU kernels can be loaded onto render engine and be executed

- **runtime/include**: The header files defining the CM APIs
    
- **runtime/lib**: The libraries implementing the CM APIs
    
- **documents/compiler**: The documents about the CM compiler usage and the CM language to write GPU kernels

- **documents/runtime**: The documents about the CM APIs to load and execute GPU kernels

- **driversi**: the media driver and IGC as low-level supported drivers to load and run GPU kernels. Release version means stripped driver binaries, while release-internal means driver binaries with symbols and other debugging functionalities, like results dumping.
    
- **examples**: Each folder under examples is an example, including host source code xxx.cpp and kernel source code xxx_genx.cpp. 

### Install Both Intel(R) Media Driver for VAAPI and Intel(R) Graphics Compiler by Script

```
    cd drivers
    sudo ./install.sh
```

### Install the Intel(R) Media Driver for VAAPI Alone

Choose release or release-internal driver package:
```
    dpkg -i intel-media-2.xxx.deb
```

Please note that: 

1. this command needs to be run in privileged mode

2. if it has this error “trying to overwrite xxx, which is also in package xxx”, it means you may have installed part of the libs before. Please add ‘--force-overwrite’ option in the installing command

3. the libva and libdrm libraries and headers, needed by building and executing CM applications, will also be installed together with media driver.

4. the media driver is now a open source project in https://github.com/intel/media-driver. You can also build from source to get the media driver libraries.

### Install Intel(R) Graphics Compiler Alone
Get the IGC package from drivers/IGC/
```
    dpkg -i IGC-xxx.deb
```

Please note that 

1. this command needs to be run in privileged mode

## Build and Execute

### Layout of a CM ( C for Media) application

Usually a CM application contains two parts: host code running on CPU and kernel code running on GPU. Take <package_root>/examples/linear_walker as an example:

- linear_walker.cpp is the host application source code. It is standard C++ source code calling the CM APIs to load the GPU kernels. It is compiled by g++ and executed on CPU.

- linear_walker_genx.cpp is the GPU kernel source code. It is written using CM language. It is compiled by CM compiler, cmc in compiler/bin, and is loaded by the host application and executed on GPU. 

### Build an example using provided make file

Change directory to any subfolder in examples, e.g. examples/linear_walker, then run make command:
 ```
    make -f ../Makefile.linux
 ```
 
Two binaries will be generated:
     
1. hw_x64.linear_walker: the executable on CPU, generated from linear_walker.cpp
     
2. linear_walker_genx.isa: the GPU kernel loaded by the CPU executable and executed on render engine on GPU, generated from linear_walker_genx.cpp

### Run the host application code

Make sure both the host application binary and the GPU kernel binary are generated, and they are in the same directory.

Run the host application binary directly.

### Build an example using command lines (advanced)

Use two command lines below to build host code
```
    g++ linear_walker.cpp -w -g -std=gnu++0x -c -rdynamic -fPIC -O0 -msse4.1 -ffloat-store \
       -I"<package_root>/runtime/include" -o linear_walker.o
    g++ linear_walker.o -fPIC <package_root>/runtime/lib/x64/igfxcmrt64.so -o linear_walker \
       -lva -ldl -lva-drm -lpthread -m64
```

Use one command line below to build kernel code
```
    <package_root>/compiler/bin/cmc linear_walker_genx.cpp -isystem <package_root>/compiler/include_llvm
```

### Build and run all the examples in the package

A shell script is provided to build and run all the examples in the examples folder:
```
    ./run_all.sh
```

## Source Code to Build This Package

- Intel(R) Media Driver for VAAPI and Intel(R) C for Media Runtime: https://github.com/intel/media-driver, commit id: c1c45706e02bf92f772d75f5bf5ee140d6b8283c

- Intel(R) C for Media compiler and examples: https://github.com/intel/cm-compiler, commit id: 450793b1326716ca44e0fdbe09f1043f25a8e31d

- Intel(R) Graphics Compiler: https://github.com/intel/intel-graphics-compiler, commit id: c24c8eb80a6d255e8d2969acbc137c388694756c

## Supported Platforms

- Intel Broadwell, Skylake, and Cannonlake

## Known Issues and Limitations
	 
##### (*) Other names and brands may be claimed as property of others.
