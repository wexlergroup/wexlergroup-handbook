---
description: >-
  Suitable for running VASP calculations on AMD CPUs. Performing higher
  calculation efficiency when using smaller CPU cores.
---

# Compiling VASP with AMD AOCC and AOCL for CPUs

## 1. Preparation



### 1.1 Compile  AOCC

AMD Optimizing C/C++ and Fortran Compilers (AOCC) is a high-performance CPU compiler for C, C++ and Fortran programming languages.&#x20;

Download the newest version from [AOCC website](https://www.amd.com/en/developer/aocc.html#downloads) and extract it into a new folder.

```
mkdir -p ~/opt/amd/AOCC
cd ~/opt/amd
tar -xvf aocc-compiler-4.2.0.tar -C ./AOCC
cd ./AOCC/aocc-compiler-4.2.0
```

Compile AOCC and follow the instruction to load the environment, and running the check script

```
bash install.sh

source /home/c.junchi/opt/amd/AOCC/setenv_AOCC.sh      # change to your path
bash AOCC-prerequisites-check.sh
```

Check whether AOCC compiliers are loaded and whether they are correctly linked to the system GNU compiler.

```
which clang clang++ flang
clang -v
clang++ -v
flang -v
```

### 1.2 Compile AOCL

AMD Optimizing CPU Libraries (AOCL) contain a set of optimized numerical libraries for AMD processors based on the AMD "Zen" core architecture and generations. Other processor families like AMD EPYC and AMD Ryzen are also supported.

Download the newest version from [AOCL website](https://www.amd.com/en/developer/aocl.html#downloads) and extract it into a new folder.

```
mkdir -p ~/opt/amd/AOCL
cd ~/opt/amd
tar -zxvf aocl-linux-aocc-4.2.0.tar.gz -C ./AOCL
cd ./AOCL
```

Create a new build folder for storage and run the installation

```
mkdir build
cd ./aocl-linux-aocc-4.2.0
bash install.sh -t ~/opt/amd/AOCL/build
```

Follow the output message to load the environment

```
# change to your path
source /home/c.junchi/opt/amd/AOCL/build/4.2.0/aocc/amd-libs.cfg
```

### 1.3 Compile HDF5 using AOCC

The introduction of HDF5 please see [1.3 at previous page](https://app.gitbook.com/o/yz8r2bSTkcNaXWzJtigI/s/9fSWrcrcGYzlf8SKeqBS/\~/changes/34/research/softwares/vasp-6.4.1-installation/compiling-vasp-with-intel-oneapi-and-hpc-toolkits-for-cpus/\~/overview).

Since we are going to compile VASP using AOCC, the HDF5 must be compiled by AOCC as well.

Create a new folder for compiling HDF5.

```
mkdir -p ~/usr/hdf5/HDF5_aocc
cd ~/usr/hdf5
```

Download the newest release version of HDF5 from the [HDF5 Source Code](https://www.hdfgroup.org/downloads/hdf5/source-code/) to your PC and upload into your linux system, extract it in the **HDF5\_aocc** folder since we are going to use AOCC to compile it. Create a new folder **build** for storage the compilation.

```
tar -xzvf hdf5-1.14.4-2.tar.gz -C ~/usr/hdf5/HDF5_aocc
cd ./HDF5_aocc/hdf5-1.14.4-2
mkdir build
```

Setting the configuration for compiling HDF5 with AOCC compiler.

```
# my example
./configurate CC=clang CXX=clang++ FC=flang --prefix=/home/c.junchi/usr/hdf5/HDF5_aocc/hdf5-1.14.4-2/build --enable-fortran --enable-cxx --enable-shared

# your operation 
./configurate CC=clang CXX=clang++ FC=flang --prefix=<your_path>/hdf5-1.14.4-2/build --enable-fortran --enable-cxx --enable-shared
```

If it failed, check whether the ZLIB is installed and check the output error messages.

After completing configuration successfully, we can now install it.

```
make -jN install     # N is the number of cores you will use for compiling
                     # higher speed with using more cores, but be careful not to disturb other users
```

Add the environment of HDF5 after successful installation.

```
# my example
export PATH="/home/c.junchi/usr/hdf5/HDF5_aocc/hdf5-1.14.4-2/build/bin:$PATH"
export LD_LIBRARY_PATH="/home/c.junchi/usr/hdf5/HDF5_aocc/hdf5-1.14.4-2/build/lib:$LD_LIBRARY_PATH"

# your operation
export PATH="<your_path>/hdf5-1.14.4-2/build/bin:$PATH"
export LD_LIBRARY_PATH="<your_path>/hdf5-1.14.4-2/build/lib:$LD_LIBRARY_PATH"
```

### 1.4 Compile OpenMPI using AOCC

The introduction of OpenMPI please see [1.4 at previous page](https://app.gitbook.com/o/yz8r2bSTkcNaXWzJtigI/s/9fSWrcrcGYzlf8SKeqBS/\~/changes/34/research/softwares/vasp-6.4.1-installation/compiling-vasp-with-intel-oneapi-and-hpc-toolkits-for-cpus/\~/overview).

Pre-check the GNU version of your system. If it is before than version 11, than you need to choose [openmpi-4.x](https://www.open-mpi.org/software/ompi/v4.1/), otherwise the MPI compiling cannot be completed.

If your system GNU version is after 11, then download [the newest OpenMPI](https://www.open-mpi.org/software/ompi/v5.0/) and extract it to a new folder.&#x20;

```
# my example
mkdir -p ~/usr/openmpi/openmpi_aocc
cd ~/usr/openmpi
wget https://download.open-mpi.org/release/open-mpi/v5.0/openmpi-5.0.4.tar.gz
tar -xzvf openmpi-5.0.4.tar.gz -C ./openmpi_aocc
cd ./openmpi_aocc/openmpi-5.0.4
```

Since we are going to compile VASP using AOCC, the OpenMPI must be compiled by AOCC as well.

```
mkdir build
./configure CC=clang CXX=clang++ FC=flang F77=flang OMPI_CC=clang OMPI_CXX=clang++ OMPI_FC=flang OMPI_F77=flang --prefix=/home/c.junchi/usr/openmpi/openmpi_aocc/openmpi-5.0.4/build

make -jN install       # N is number of the cpu cores that you want to use
```

Add the environment of OpenMPI.

```
# my example
export PATH="/home/c.junchi/usr/openmpi/openmpi_aocc/openmpi-5.0.4/build/bin:$PATH"
export LD_LIBRARY_PATH="/home/c.junchi/usr/openmpi/openmpi_aocc/openmpi-5.0.4/build/lib:$LD_LIBRARY_PATH"

# your operation
export PATH="<your_path>/openmpi-5.0.4/build/bin:$PATH"
export LD_LIBRARY_PATH="<your_path>/openmpi-5.0.4/build/lib:$LD_LIBRARY_PATH"
```

## 2. VASP Installation

### Compile VASP using AOCC + OpenMPI with the support of AOCL

