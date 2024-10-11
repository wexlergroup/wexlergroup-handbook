---
description: >-
  Suitable for running VASP calculations on AMD CPUs. Performing higher
  calculation efficiency when using smaller CPU cores.
---

# Compiling VASP with AMD AOCC and AOCL for CPUs

## 1. Preparation

### 1.1 Compile  AOCC

AMD Optimizing C/C++ and Fortran Compilers (AOCC) is a high-performance CPU compiler for C, C++, and Fortran programming languages.&#x20;

Download the newest version from the [AOCC website](https://www.amd.com/en/developer/aocc.html#downloads) and extract it into a new folder.

```
mkdir -p ~/opt/amd/AOCC
cd ~/opt/amd
tar -xvf aocc-compiler-4.2.0.tar -C ./AOCC
cd ./AOCC/aocc-compiler-4.2.0
```

Compile AOCC, load the environment, and run the check script.

```
bash install.sh

source /home/c.junchi/opt/amd/AOCC/setenv_AOCC.sh      # change to your path
bash AOCC-prerequisites-check.sh
```

Check whether AOCC compilers are loaded and correctly linked to the system GNU compiler.

```
which clang clang++ flang
clang -v
clang++ -v
flang -v
```

### 1.2 Compile AOCL

AMD Optimizing CPU Libraries (AOCL) contain a set of optimized numerical libraries for AMD processors based on the AMD "Zen" core architecture and generations. Other processor families like AMD EPYC and AMD Ryzen are also supported.

Download the newest version from the [AOCL website](https://www.amd.com/en/developer/aocl.html#downloads) and extract it into a new folder.

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
# my example
source /home/c.junchi/opt/amd/AOCL/build/4.2.0/aocc/amd-libs.cfg

# use your path
source <your path>/opt/amd/AOCL/build/4.2.0/aocc/amd-libs.cfg
```

### 1.3 Compile HDF5 using AOCC

For the introduction of HDF5 please see [1.3 on the previous page](https://app.gitbook.com/o/yz8r2bSTkcNaXWzJtigI/s/9fSWrcrcGYzlf8SKeqBS/\~/changes/34/research/softwares/vasp-6.4.1-installation/compiling-vasp-with-intel-oneapi-and-hpc-toolkits-for-cpus/\~/overview).

Since we will compile VASP using AOCC, the HDF5 must also be compiled by AOCC.

Create a new folder for compiling HDF5.

```
mkdir -p ~/usr/hdf5/HDF5_aocc
cd ~/usr/hdf5
```

Download the newest release version of HDF5 from the [HDF5 Source Code](https://www.hdfgroup.org/downloads/hdf5/source-code/) to your PC and upload it into your Linux system, extract it in the **HDF5\_aocc** folder since we are going to use AOCC to compile it. Create a new folder **build** for storage of the compilation.

```
tar -xzvf hdf5-1.14.4-2.tar.gz -C ~/usr/hdf5/HDF5_aocc
cd ./HDF5_aocc/hdf5-1.14.4-2
mkdir build
```

Setting the configuration for compiling HDF5 with the AOCC compiler.

```
# my example
./configurate CC=clang CXX=clang++ FC=flang --prefix=/home/c.junchi/usr/hdf5/HDF5_aocc/hdf5-1.14.4-2/build --enable-fortran --enable-cxx --enable-shared

# your operation 
./configurate CC=clang CXX=clang++ FC=flang --prefix=<your_path>/hdf5-1.14.4-2/build --enable-fortran --enable-cxx --enable-shared
```

If it fails, check whether the ZLIB is installed and check the output error messages.

After completing the configuration successfully, we can now install it.

```
make -jN install     # N is the number of cores you will use for compiling
                     # higher speed with using more cores, but be careful not to disturb other users
```

Add the HDF5 environment after successful installation.

```
# my example
export PATH="/home/c.junchi/usr/hdf5/HDF5_aocc/hdf5-1.14.4-2/build/bin:$PATH"
export LD_LIBRARY_PATH="/home/c.junchi/usr/hdf5/HDF5_aocc/hdf5-1.14.4-2/build/lib:$LD_LIBRARY_PATH"

# your operation
export PATH="<your_path>/hdf5-1.14.4-2/build/bin:$PATH"
export LD_LIBRARY_PATH="<your_path>/hdf5-1.14.4-2/build/lib:$LD_LIBRARY_PATH"
```

### 1.4 Compile OpenMPI using AOCC

For the introduction of OpenMPI please see [1.4 on the previous page](https://app.gitbook.com/o/yz8r2bSTkcNaXWzJtigI/s/9fSWrcrcGYzlf8SKeqBS/\~/changes/34/research/softwares/vasp-6.4.1-installation/compiling-vasp-with-intel-oneapi-and-hpc-toolkits-for-cpus/\~/overview).

Pre-check the GNU version of your system. If it is before version 11, then you need to choose [openmpi-4.x](https://www.open-mpi.org/software/ompi/v4.1/), otherwise the MPI compiling cannot be completed.

If your system GNU version is after 11, then download [the newest OpenMPI](https://www.open-mpi.org/software/ompi/v5.0/) and extract it to a new folder.&#x20;

```
# my example
mkdir -p ~/usr/openmpi/openmpi_aocc
cd ~/usr/openmpi
wget https://download.open-mpi.org/release/open-mpi/v5.0/openmpi-5.0.4.tar.gz
tar -xzvf openmpi-5.0.4.tar.gz -C ./openmpi_aocc
cd ./openmpi_aocc/openmpi-5.0.4
```

Since we will compile VASP using AOCC, the OpenMPI must also be compiled by AOCC.

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

Download the VASP installation package and put it in a new folder.

```bash
mkdir -p ~/software/vasp
cd ~/software/vasp
```

Here we only demonstrate one example of how to compile HFD5-supported VASP using OpenMP+OpenMPI with AOCL. If you want to compile VASP with other options (e.g., without OpenMPI), please go back to [compiling VASP using the Intel compiler](compiling-vasp-with-intel-oneapi-and-hpc-toolkits-for-cpus.md#id-2.-vasp-installation) and read 2.1.

### Compile HFD5-supported VASP using OpenMP+OpenMPI with AOCL

First, check again whether you have uploaded the environment of AOCC, AOCL, and OpenMPI (see preparation 1.1, 1.2, 1.4).

Unzip the VASP compressed package and rename it.

```
# create a new folder
tar -xvf vasp.6.4.1.tar
mv vasp.6.4.1 vasp.6.4.1_aocc_aocl_omp_ompi
cd vasp.6.4.1_aocc_aocl_omp_ompi
```

Prepare makefile.include file by copying from[ makefile.include.aocc\_ompi\_aocl\_omp](https://www.vasp.at/wiki/index.php/Makefile.include.aocc\_ompi\_aocl\_omp).

Modify the content in makefile.include as following:

```
# my example
AMDBLIS_ROOT ?= /home/c.junchi/opt/amd/AOCL/build/4.2.0/aocc
AMDLIBFLAME_ROOT ?= /home/c.junchi/opt/amd/AOCL/build/4.2.0/aocc
AMDSCALAPACK_ROOT ?= /home/c.junchi/opt/amd/AOCL/build/4.2.0/aocc
AMDFFTW_ROOT  ?= /home/c.junchi/opt/amd/AOCL/build/4.2.0/aocc
HDF5_ROOT  ?= /home/c.junchi/usr/hdf5/HDF5_aocc/hdf5-1.14.4-2/buil
```

```
# check whether you have loaded the environments of aocc, aocl, and OpenMPI
which clang clang++ flang mpif90

# compiling VASP
make DEPS=1 -jN <target>                                 # <target> can be {std, gam, ncl or all}

# test VASP
make -jN test
```

Setting the environment variables of the compiler, programs, and libraries you have used permanently by writing in your `~/.bashrc` file or temporally by writing in your job submit script `vasp.sh`
