---
description: >-
  Compiling VASP using Intel tools is one of the most popular and convenient
  ways.
---

# Compiling VASP with Intel oneAPI and HPC Toolkits for CPUs

## Quick Start

*   ### For installing the most basic version of VASP

    Read 1, 1.1, 1.2, 2, and 2.1
*   ### For compiling VASP using OpenMP in oneAPI

    Read 1, 1.1, 1.2, 2, and 2.2
*   ### For hybried OpenMP+OpenMPI VASP Compiling

    Read 1, 1.1, 1.2, 1,4, 2, and 2.3
*   ### For how to add HDF5 Library

    Read 1.3

## 1. Preparation

If the administrator has installed the following two Intel tools for all users in the HPC you are using, then you can skip the first two steps. Load the environment modules your administrator setted or find the `setvars.sh` file and run it. Don't forget to check whether the `$PATH`, `$LD_LIBRARY_PATH`, and `$MKLROOT` environment variables are loaded as shown in the steps 1.1 and 1.2.

```
# useful shell commands
module avail                      # check the available modules you can load
module load <module_name>         # load the aimed module environment
find <path> -name <file_name>     # search the file under all the files under the path
locate <file_name>                # find the location of the file 
```

### 1.1 Compile Intel oneAPI Base Toolkit (required)

Download the newest version file from the [get the Intel oneAPI Base Toolkit](https://www.intel.com/content/www/us/en/developer/tools/oneapi/base-toolkit-download.html) to your system. For example, cllick Linux-and-Online Installer buttons and go down to the **Command Line Download** line. Copy the following command and run on your terminal.\
Your can remove `--silent`  optition if you want to customize the installation yourself.

```
wget https://registrationcenter-download.intel.com/akdlm/IRC_NAS/9a98af19-1c68-46ce-9fdd-e249240c7c42/l_BaseKit_p_2024.2.0.634.sh
bash ./l_BaseKit_p_2024.2.0.634.sh -a --silent --cli --eula accept
```

By default installation, it will generate a new folder, namly **intel**, under your home directory. It is recommened to move it to a specific folder to storage all the compillers. Here is my example:

```
# my example
cd ~
mkdir -p ./opt/intel/oneAPI
mv ./intel ./opt/intel/oneAPI
```

Then, activate the oneAPI by running it's variables setting bash file:

```
# my example 
source /home/c.junchi/opt/intel/oneAPI/intel/oneapi/setvars.sh --force

# your operation
source (your_path)/intel/oneapi/setvars.sh --force
```

Check whether the `$PATH`, `$LD_LIBRARY_PATH`, and `$MKLROOT` environment variables have been added:

```
echo -e "PATH:\n${PATH//:/\\n}\n"
echo -e "LD_LIBRARY_PATH:\n${LD_LIBRARY_PATH//:/\\n}"
echo -e "MKLROOT:\n${MKLROOT//:/\\n}"
```

### 1.2 Compile Intel HPC Toolkit (required)

Download the newest version file from the [get the Intel HPC Toolkit](https://www.intel.com/content/www/us/en/developer/tools/oneapi/hpc-toolkit-download.html) to your system. Similar as above.

```
wget https://registrationcenter-download.intel.com/akdlm/IRC_NAS/d4e49548-1492-45c9-b678-8268cb0f1b05/l_HPCKit_p_2024.2.0.635.sh
bash ./l_HPCKit_p_2024.2.0.635.sh
```

Again, after successfully run the above commands, an **intel** name folder generated under your home directory. Move it to a suitable path as well.

```
# my example
cd ~
mkdir -p ./opt/intel/HPC_toolkit
mv ./intel ./opt/intel/HPC_toolkit
```

Activate HPC Toolkit:

```
# my example 
source /home/c.junchi/opt/intel/HPC_toolkit/intel/oneapi/setvars.sh --force

# your operation
source (your_path)/intel/oneapi/setvars.sh --force
```

Check whether the `$PATH`, `$LD_LIBRARY_PATH`, and `$MKLROOT` environment variables have been added by running the same command as above.

```
echo -e "PATH:\n${PATH//:/\\n}\n"
echo -e "LD_LIBRARY_PATH:\n${LD_LIBRARY_PATH//:/\\n}\n"
echo -e "MKLROOT:\n${MKLROOT//:/\\n}\n"
```

### 1.3 Compile HDF5 using oneAPI (recommended)

HDF5 library is a high-performance data management and storage suite which is highly recommended by VASP Group. It is needed for reading and writing VASP inputs and outputs files like vaspin.h5, vaspout.h5, and vaspwave.h5.

Create a new folder for compiling HDF5.

```
# my example
mkdir -p ~/usr/hdf5/HDF5_intel
cd ~/usr/hdf5
```

Download the newest release version of HDF5 from the [HDF5 Source Code](https://www.hdfgroup.org/downloads/hdf5/source-code/) to your PC and upload into your linux system, extract it in the **HDF5\_intel** folder since we are going to use oneAPI to compile it. Create a new folder **build** for storage the compilation.

```
tar -xzvf hdf5-1.14.4-2.tar.gz -C ~/usr/hdf5/HDF5_intel
cd ./HDF5_intel/hdf5-1.14.4-2
mkdir build
```

Setting the configuration for compiling HDF5 with oneAPI compiler (keep the same compiler as you will use for VASP installation).

```
# my example
./configurate CC=icx CXX=icpx FC=ifort --prefix=/home/c.junchi/usr/hdf5/HDF5_intel/hdf5-1.14.4-2/build --enable-fortran --enable-cxx --enable-shared

# your operation 
./configurate CC=icx CXX=icpx FC=ifort --prefix=<your_path>/hdf5-1.14.4-2/build --enable-fortran --enable-cxx --enable-shared
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
export PATH="/home/c.junchi/usr/hdf5/HDF5_intel/hdf5-1.14.4-2/build/bin:$PATH"
export LD_LIBRARY_PATH="/home/c.junchi/usr/hdf5/HDF5_intel/hdf5-1.14.4-2/build/lib:$LD_LIBRARY_PATH"

# your operation
export PATH="<your_path>/hdf5-1.14.4-2/build/bin:$PATH"
export LD_LIBRARY_PATH="<your_path>/hdf5-1.14.4-2/build/lib:$LD_LIBRARY_PATH"
```

### 1.4 Compile OpenMPI using oneAPI (optional)

The OpenMPI project maintain a high performance message passing library for high performance computing. Compiled to use MPI ranks parallelization and OpenMP threading combinedly, it can improve the performance of machine in many cases like using [large CPU cores or GPUs](https://www.vasp.at/wiki/index.php/Combining\_MPI\_and\_OpenMP) to run VASP calculations.

Pre-check the GNU version of your system. If it is before than version 11, than you need to choose [openmpi-4.x](https://www.open-mpi.org/software/ompi/v4.1/), otherwise the MPI compiling cannot be completed.

If your system GNU version is after 11, then download [the newest OpenMPI](https://www.open-mpi.org/software/ompi/v5.0/) and extract it to a new folder.

```
# my example
mkdir -p ~/usr/openmpi/openmpi_intel
cd ~/usr/openmpi
wget https://download.open-mpi.org/release/open-mpi/v5.0/openmpi-5.0.4.tar.gz
tar -xzvf openmpi-5.0.4.tar.gz -C ./openmpi_intel
cd ./openmpi_intel/openmpi-5.0.4
```

Compile MPI by using oneAPI (keep the same compiler as you will use for VASP installation).

```
mkdir build
./configure CC=icx CXX=icpx FC=ifort F77=ifort OMPI_CC=icx OMPI_CXX=icpx OMPI_FC=ifort OMPI_F77=ifort --prefix=/home/c.junchi/usr/openmpi/openmpi_intel/openmpi-5.0.4/build

make -jN install       # N is number of the cpu cores that you want to use
```

Add the environment of OpenMPI.

```
# my example
export PATH="/home/c.junchi/usr/openmpi/openmpi_intel/openmpi-5.0.4/build/bin:$PATH"
export LD_LIBRARY_PATH="/home/c.junchi/usr/openmpi/openmpi_intel/openmpi-5.0.4/build/lib:$LD_LIBRARY_PATH"

# your operation
export PATH="<your_path>/openmpi-5.0.4/build/bin:$PATH"
export LD_LIBRARY_PATH="<your_path>/openmpi-5.0.4/build/lib:$LD_LIBRARY_PATH"
```

## 2. VASP Installation

Download the VASP installation package to a new folder.

```
mkdir -p ~/software/vasp
cd ~/software/vasp
```

Here are several ways that you can choose to compile VASP using Intel oneAPI tools.

### 2.1 Compile VASP using oneAPI with the support of MKL

This is the most basic version of VASP installation. First, extract VASP installation files and rename it as `vasp.6.4.1_intel`.

```
tar -xvf vasp.6.4.1.tar
mv vasp.6.4.1 vasp.6.4.1_intel
cd vasp.6.4.1_intel
```

Always find the newest corresponding **makefile.include.xxx** file from [VASP Wiki](https://www.vasp.at/wiki/index.php/Makefile.include). Here we are going to copy [makefile.include.intel](https://www.vasp.at/wiki/index.php/Makefile.include.intel) to a **makefile.include** file under our VASP folder for compilling.

```
vi makefile.include     # create a makefile.include under vasp.6.4.1_intel folder and copy the content from makefile.include.intel
```

Make the following necessary changes in your makefile.include

```
CC_LIB = icx
CXX_PARS = icpx
```

Make the following optional changes in your **makefile.include** when you meet the conditions as described hehind the comment #.

```
FCL += -mkl=sequential                              # if you are using Intel Parallel Studio rather than Intel oneAPI

CPP_OPTIONS+= -DVASP_HDF5                           # uncomment only when you installed the hdf5
HDF5_ROOT  ?= <your_path>/hdf5-1.14.4-2/build       # uncomment only when you installed the hdf5 and keep the path as same as yours in step 3
LLIBS      += -L$(HDF5_ROOT)/lib -lhdf5_fortran     # uncomment only when you installed the hdf5
INCS       += -I$(HDF5_ROOT)/include                # uncomment only when you installed the hdf5
```

Check whether you have loaded the environment of oneAPI.

Run the `make` command to generate the specific [VASP executables](https://www.vasp.at/wiki/index.php/Installing\_VASP.6.X.X#Step\_3:\_Make) that you want.

```
which icx icpx

make DEPS=1 -jN <target>       # <target> can be {std, gam, ncl or all}
```

Once you successfully compilied VASP, don't forget to run the test suite.

```
make -jN test
```

Setting the environment variables of the compiler, pragrams, and libraries you have used permanently by writing in your `~/.bashrc` file or temporally by writing in your job submit script `vasp.sh`&#x20;

### 2.2 Compile VASP using OpenMP embeded in oneAPI with the support of MKL

Almost the same as the step 2.1 but the **makefile.include** need to be copied from [makefile.include.intel\_omp](https://www.vasp.at/wiki/index.php/Makefile.include.intel\_omp) instead.

```
# create a new folder
tar -xvf vasp.6.4.1.tar
mv vasp.6.4.1 vasp.6.4.1_intel_omp
cd vasp.6.4.1_intel_omp
```

```
# create a makefile.include under vasp.6.4.1_intel_omp folder 
# and copy the content from makefile.include.intel_omp
vi makefile.include
```

```
# make the following necessary modifications in the makefile.include
CC_LIB = icx
CXX_PARS = icpx
```

```
# make the following optional modifications only when you meet the conditions behind the #
FCL += -mkl=sequential                                   # if you are using Intel Parallel Studio rather than Intel oneAPI

CPP_OPTIONS+= -DVASP_HDF5                                # uncomment only when you installed the hdf5
HDF5_ROOT  ?= <your_path>/hdf5-1.14.4-2/build            # uncomment only when you installed the hdf5 and keep the path as same as yours in step 3
LLIBS      += -L$(HDF5_ROOT)/lib -lhdf5_fortran          # uncomment only when you installed the hdf5
INCS       += -I$(HDF5_ROOT)/include                     # uncomment only when you installed the hdf5
```

```
# check whether you have loaded the oneAPI environment
which icx icpx mpiifort

# compiling VASP
make DEPS=1 -jN <target>                                 # <target> can be {std, gam, ncl or all}

# test VASP
make -jN test
```

Setting the environment variables of the compiler, pragrams, and libraries you have used permanently by writing in your `~/.bashrc` file or temporally by writing in your job submit script `vasp.sh`&#x20;

### 2.3 Compile VASP using OpenMP + OpenMPI with the support of MKL

It is trully worthwhile to install [a hybird OpenMP + OpenMPI compiled VASP](https://cmp.univie.ac.at/publications/publications-details/?tx\_univiepure\_univiepure%5Buuid%5D=63c02b3d-bfd3-4dc2-8f21-8eedff2b185c\&tx\_univiepure\_univiepure%5Bwhat2show%5D=publ\&tx\_univiepure\_univiepure%5Baction%5D=show\&tx\_univiepure\_univiepure%5Bcontroller%5D=Pure\&cHash=8e7a7d04973ab6648944d364899a441c) and test the most suitable parallel parameters for your HPC to maximize the calculation performance.

```
# create a new folder
tar -xvf vasp.6.4.1.tar
mv vasp.6.4.1 vasp.6.4.1_intel_ompi_mkl_omp
cd vasp.6.4.1_intel_ompi_mkl_omp
```

Writing the makefile.include by copying from [makefile.include.intel\_ompi\_mkl\_omp](https://www.vasp.at/wiki/index.php/Makefile.include.intel\_ompi\_mkl\_omp)

```
# create a makefile.include under vasp.6.4.1_intel_ompi_mkl_omp folder 
# and copy the content from makefile.include.intel_ompi_mkl_omp
vi makefile.include
```

```
# make the following necessary modifications in the makefile.include
CC_LIB = icx
CXX_PARS = icpx
```

```
# make the following optional modifications only when you meet the conditions behind the #
FCL += -mkl=sequential                                   # if you are using Intel Parallel Studio rather than Intel oneAPI

CPP_OPTIONS+= -DVASP_HDF5                                # uncomment only when you installed the hdf5
HDF5_ROOT  ?= <your_path>/hdf5-1.14.4-2/build            # uncomment only when you installed the hdf5 and keep the path as same as yours in step 3
LLIBS      += -L$(HDF5_ROOT)/lib -lhdf5_fortran          # uncomment only when you installed the hdf5
INCS       += -I$(HDF5_ROOT)/include                     # uncomment only when you installed the hdf5
```

```
# check whether you have loaded the environments of oneAPI and OpenMPI
which icx icpx mpif90

# compiling VASP
make DEPS=1 -jN <target>                                 # <target> can be {std, gam, ncl or all}

# test VASP
make -jN test
```

Setting the environment variables of the compiler, pragrams, and libraries you have used permanently by writing in your `~/.bashrc` file or temporally by writing in your job submit script `vasp.sh`&#x20;
