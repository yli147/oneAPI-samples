﻿# `ISO3DFD DPC++` Sample

The ISO3DFD sample refers to Three-Dimensional Finite-Difference Wave Propagation in Isotropic Media.  It is a three-dimensional stencil to simulate a wave propagating in a 3D isotropic medium. It shows some of the more common challenges when targeting SYCL devices (GPU/CPU) in more complex applications.

For comprehensive instructions see the [DPC++ Programming](https://software.intel.com/en-us/oneapi-programming-guide) and search based on relevant terms noted in the comments.

| Optimized for                       | Description
|:---                               |:---
| OS                                | Linux* Ubuntu* 18.04; Windows 10
| Hardware                          | Skylake with GEN9 or newer
| Software                          | Intel® oneAPI DPC++/C++ Compiler
| What you will learn               | How to offload the computation to GPU using Intel&reg; oneAPI DPC++/C++ Compiler
| Time to complete                  | 15 minutes

Performance number tabulation

| iso3dfd sample                      | Performance data
|:---                               |:---
| Scalar baseline -O2               | 1.0
| SYCL                              | 2x speedup


## Purpose

ISO3DFD is a finite difference stencil kernel for solving the 3D acoustic isotropic wave equation, which can be used as a proxy for propagating a seismic wave. In this sample, kernels are implemented as 16th order in space, with symmetric coefficients, and 2nd order in time scheme without boundary conditions. Using Data Parallel C++, the sample can explicitly run on the GPU and/or CPU to propagate a seismic wave, which is a compute intensive task.

The code will first attempt to execute on an available GPU and fallback to the system's CPU if a compatible GPU is not detected. By default, the output will print the device name where the DPC++ code ran along with the grid computation metrics - flops and effective throughput. For validating results, a serial version of the application will be run on CPU, and results will be compared to the DPC++ version.

For a detailed [code walkthrough](https://software.intel.com/content/www/us/en/develop/documentation/explore-dpcpp-samples-from-intel/top.html#top_STEP4_ISO3DFD).

## Key Implementation Details

The basic DPC++ implementation explained in the code includes the use of the following :
* DPC++ local buffers and accessors (declare local memory buffers and accessors to be accessed and managed by each DPC++ workgroup)
* Code for Shared Local Memory (SLM) optimizations
* DPC++ kernels (including parallel_for function and nd-range<3> objects)
* DPC++ queues (including custom device selector and exception handlers)


## License

Code samples are licensed under the MIT license. See
[License.txt](https://github.com/oneapi-src/oneAPI-samples/blob/master/License.txt) for details.

Third party program Licenses can be found here: [third-party-programs.txt](https://github.com/oneapi-src/oneAPI-samples/blob/master/third-party-programs.txt)

## Building the `ISO3DFD` Program for CPU and GPU


> **Note**: If you have not already done so, set up your CLI
> environment by sourcing  the `setvars` script located in
> the root of your oneAPI installation.
>
> Linux Sudo: . /opt/intel/oneapi/setvars.sh
>
> Linux User: . ~/intel/oneapi/setvars.sh
>
> Windows: C:\Program Files(x86)\Intel\oneAPI\setvars.bat
>
>For more information on environment variables, see Use the setvars Script for [Linux or macOS](https://www.intel.com/content/www/us/en/develop/documentation/oneapi-programming-guide/top/oneapi-development-environment-setup/use-the-setvars-script-with-linux-or-macos.html), or [Windows](https://www.intel.com/content/www/us/en/develop/documentation/oneapi-programming-guide/top/oneapi-development-environment-setup/use-the-setvars-script-with-windows.html).

### Include Files
The include folder is located at `%ONEAPI_ROOT%\dev-utilities\latest\include` on your development system".

### Running Samples In DevCloud
If running a sample in the Intel DevCloud, remember that you must specify the compute node (CPU, GPU) and run in batch or interactive mode. For more information, see the [Intel® oneAPI Base Toolkit Get Started Guide] (https://devcloud.intel.com/oneapi/get-started/base-toolkit/)


### Using Visual Studio Code*  (Optional)

You can use Visual Studio Code (VS Code) extensions to set your environment, create launch configurations,
and browse and download samples.

The basic steps to build and run a sample using VS Code include:
 - Download a sample using the extension **Code Sample Browser for Intel oneAPI Toolkits**.
 - Configure the oneAPI environment with the extension **Environment Configurator for Intel oneAPI Toolkits**.
 - Open a Terminal in VS Code (**Terminal>New Terminal**).
 - Run the sample in the VS Code terminal using the instructions below.

To learn more about the extensions and how to configure the oneAPI environment, see
[Using Visual Studio Code with Intel® oneAPI Toolkits](https://software.intel.com/content/www/us/en/develop/documentation/using-vs-code-with-intel-oneapi/top.html).

After learning how to use the extensions for Intel oneAPI Toolkits, return to this readme for instructions on how to build and run a sample.

### On a Linux* System
Perform the following steps:
1. Build the program using the following `cmake` commands.
```
$ mkdir build
$ cd build
$ cmake ..
$ make -j
```

> Note: by default, executable is build with kernel with direct global memory usage. You can build the kernel with shared local memory (SLM) buffers with the following:
```
cmake -DSHARED_KERNEL=1 ..
make -j
```

2. Run the program :
    ```
    make run
    ```
> Note: for selecting CPU as a SYCL device, use `make run_cpu`

3. Clean the program using:
    ```
    make clean
    ```

If an error occurs, you can get more details by running `make` with
the `VERBOSE=1` argument:
``make VERBOSE=1``
For more comprehensive troubleshooting, use the Diagnostics Utility for
Intel® oneAPI Toolkits, which provides system checks to find missing
dependencies and permissions errors.
[Learn more](https://software.intel.com/content/www/us/en/develop/documentation/diagnostic-utility-user-guide/top.html).

### On a Windows* System Using Visual Studio* Version 2017 or Newer
- Build the program using VS2017 or VS2019
    - Right-click on the solution file and open using either VS2017 or VS2019 IDE.
    - Right-click on the project in Solution Explorer and select Rebuild.
    - From the top menu, select Debug -> Start without Debugging.

- Build the program using MSBuild
     - Open "x64 Native Tools Command Prompt for VS2017" or "x64 Native Tools Command Prompt for VS2019"
     - Run the following command: `MSBuild iso3dfd_dpcpp.sln /t:Rebuild /p:Configuration="Release"`

## Running the Sample
```
make run
```

### Application Parameters
You can modify the ISO3DFD parameters from the command line.
   * Configurable Application Parameters

	Usage: src/iso3dfd.exe n1 n2 n3 b1 b2 b3 Iterations [omp|sycl] [gpu|cpu]

 	n1 n2 n3                               : Grid sizes for the stencil
 	b1 b2 b3   OR         		       : cache block sizes for cpu openmp version.
 	b1 b2                 	               : Thread block sizes in X and Y dimensions for SYCL version.
          and b3                               : size of slice of work in Z dimension for SYCL version.
 	Iterations                             : No. of timesteps.
 	[omp|sycl]                             : Optional: Run the OpenMP or the SYCL variant. Default is to use both for validation
 	[gpu|cpu]                              : Optional: Device to run the SYCL version Default is to use the GPU if available, if not fallback to CPU

### Example of Output
```
Grid Sizes: 256 256 256
Memory Usage: 230 MB
 ***** Running C++ Serial variant *****
Initializing ...
--------------------------------------
time         : 2.92984 secs
throughput   : 57.2632 Mpts/s
flops        : 3.49306 GFlops
bytes        : 0.687159 GBytes/s

--------------------------------------

--------------------------------------
 ***** Running SYCL variant *****
Initializing ...
 Running on Intel(R) Gen9
 The Device Max Work Group Size is : 256
 The Device Max EUCount is : 48
 The blockSize x is : 32
 The blockSize y is : 8
 Using Global Memory Kernel
--------------------------------------
time         : 0.597494 secs
throughput   : 280.793 Mpts/s
flops        : 17.1284 GFlops
bytes        : 3.36952 GBytes/s

--------------------------------------

--------------------------------------
Final wavefields from SYCL device and CPU are equivalent: Success
--------------------------------------
```

