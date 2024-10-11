# Compiling VASP on M1 Mac

## 1. Preparations

### 1.1 Install Xcode command line tools

Check whether Xcode command line tools are installed in your Mac machine by running the command in the terminal:

```bash
xcode-select -p
```

If it doesn't output the installation path, install it by running:

```bash
xcode-select --install
```

### 1.2 Install Homebrew

Install [Homebrew](https://brew.sh/) by copy the following command in your macOS terminal and run it:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### 1.3 Install dependent compilers and libraries

* GNU compilers
* Open MPI
* ScaLAPACK library
* FFTW library
* QD library
* OpenBLAS library
* HDF5 library (optional but recommended by VASP)

Install required dependencies by using Homebrew for installing VASP:

```bash
brew install gcc openmpi scalapack fftw qd openblas

brew install hdf5   # optional but recommended by VASP
```

Check whether these dependencies are successfully installed:

```bash
# Check the installed GNU compilers
which gcc-14 g++-14    # Homebrew install version-14 by default

# Outputs
/opt/homebrew/bin/gcc-14
/opt/homebrew/bin/g++-14
```

```bash
# Check the installed Open MPI
which mpirun

# Outputs
/opt/homebrew/mpirun
```

```bash
# Check the installed libraries
brew list scalapack fftw qd openblas
brew list hdf5                        # if you installed

# Outputs
/opt/homebrew/Cellar/xxx              # xxx are the details of the libraries
```

## 2. VASP Installation

Download the VASP compressed package and unzip it into a new folder.

```bash
mkdir -p ~/software/vasp
cd ~/software/vasp

tar -xvf ./vasp.6.4.1.tar
mv vasp.6.4.1 vasp.6.4.1_gnu
cd vasp.6.4.1_gnu
```

Always find the newest corresponding **makefile.include.xxx** file from [VASP Wiki](https://www.vasp.at/wiki/index.php/Makefile.include). Here we are going to copy the archetypical file[ makefile.include.gnu\_omp](https://www.vasp.at/wiki/index.php/Makefile.include.gnu\_omp) to a **makefile.include** file under our VASP folder (`~/software/vasp/vasp.6.4.1_gnu)` for compiling.

Modify your **makefile.include** file as follows:

* Add a new option in the last line of `CPP_OPTIONS`

```
CPP_OPTIONS = -DHOST=\"LinuxGNU\" \
              -DMPI -DMPI_BLOCK=8000 -Duse_collective \
              -DscaLAPACK \
              -DCACHE_SIZE=4000 \
              -Davoidalloc \
              -Dvasp6 \
              -Duse_bse_te \
              -Dtbdyn \
              -Dfock_dblbuf \
              -D_OPENMP  -Dqd_emulate              # add -Dqd_emulate after -D_OPENMP
```

* Specify the compiler

```
# Find these lines in your makefile.include file
CPP         = gcc -E -C -w $*$(FUFFIX) >$*$(SUFFIX) $(CPP_OPTIONS)
CC_LIB      = gcc
CXX_PARS    = g++

# Change "gcc" to "gcc-14" and "g++" to "g++-14"
CPP         = gcc-14 -E -C -w $*$(FUFFIX) >$*$(SUFFIX) $(CPP_OPTIONS)
CC_LIB      = gcc-14
CXX_PARS    = g++-14
```

* Link the installed libraries

```
# Find these lines in your makefile.include file
OPENBLAS_ROOT ?= /path/to/your/openblas/installation
SCALAPACK_ROOT ?= /path/to/your/scalapack/installation
FFTW_ROOT  ?= /path/to/your/fftw/installation

# Change them by providing the specific path
OPENBLAS_ROOT ?= /opt/homebrew/Cellar/openblas/0.3.28      # double-check the version you installed
SCALAPACK_ROOT ?= /opt/homebrew
FFTW_ROOT ?= /opt/homebrew
```

* Only if you installed the `HDF5` library

```
# Find the lines for linking hdf5 in your makefile.include file
#CPP_OPTIONS+= -DVASP_HDF5
#HDF5_ROOT  ?= /path/to/your/hdf5/installation
#LLIBS      += -L$(HDF5_ROOT)/lib -lhdf5_fortran
#INCS       += -I$(HDF5_ROOT)/include

# Uncomment them and provide the path
CPP_OPTIONS+= -DVASP_HDF5
HDF5_ROOT  ?= /opt/homebrew
LLIBS      += -L$(HDF5_ROOT)/lib -lhdf5_fortran
INCS       += -I$(HDF5_ROOT)/include
```

* Update the parser section&#x20;

```
# Find the parser block in your makefile.include file
# For the parser library
CXX_PARS    = g++-14
LLIBS       = -lstdc++

# Delate one line and add new lines
# For the parser library
CXX_PARS    = g++-14                  # keep this line
LLIBS       = -lstdc++                # delete this line
LIBS += parser                        # add this line
LLIBS = -Lparser -lparser -lstdc++    # add this line
QD ?= /opt/homebrew                   # add this line
LLIBS += -L$(QD)/lib -lqdmod -lqd     # add this line
INCS += -I$(QD)/include/qd            # add this line
```

* Change `OBJECTS_LIB` line

```
# Find OBJECTS_LIB line in your makefile.include file
OBJECTS_LIB = linpack_double.o

# Append getshmem.o to OBJECTS_LIB
OBJECTS_LIB = linpack_double.o getshmem.o
```

Modify `~/software/vasp/vasp.6.4.1_gnu/src/parser/makefile:`

```
# Find the line
ar vq libparser.a $(CPPOBJ_PARS) $(COBJ_PARS) locproj.tab.h

# Delete locproj.tab.h
ar vq libparser.a $(CPPOBJ_PARS) $(COBJ_PARS)
```

Modify `~/software/vasp/vasp.6.4.1_gnu/src/lib/getshmem.c:`

```
# Find the location
/*
 * get a shared memory segment
 * input: size (fortran integer*8)
 *output: shmem id
*/

# Add a new line behind
/*
 * get a shared memory segment
 * input: size (fortran integer*8)
 *output: shmem id
*/
#define SHM_NORESERVE 0 // this line was added
```

Compiling VASP

```bash
make -jN <target>       # <target> can be {std, gam, ncl or all}
```

Test VASP executable files

```bash
make -jN test
```









