# VASP(6.4.1) Installation

## Introduction

This tutorial is written from a non-root user's perspective for learning how to compile VASP from the very beginning of install the required compilers, programs, and libraries. However it can also be benefit to root-user to practice with a little change of the command.  Please attention, for readers who want to compile VASP in their external HPC account, the compiler you installed under your home directory can not be recognized when submitting jobs to computing nodes. You should always use the compiler that was installed by the administrator.

## Outline

1. Choose or install one compiler (GNU, Intel oneAPI, AMD AOCC, NVIDIA HPC\_SDK etc).
2. Choose or install additional libraries and programs that you want to extended in VASP (HDF5, DFT-D4, Wannier90, etc).
3. Set up the environment for compelling (export `$PATH` and `$LD_LIBRARY_PATH`, etc).
4. Download and extract the VASP folder (ask from license holder in your group).
5. Choose the right type of makefile.include file and make necessary modifications.
6. Compile and test
7. You made it! Good job! :thumbsup::thumbsup::thumbsup:

## Cluster used here

### Bear at Wexler Group

* Operating system: Ubuntu 22.04.4 LTS
* CPUs: Intel(R) Xeon(R) Gold 6338 CPU @ 2.00GHz
* GNU compiler (system): gcc version 11.4.0

## Useful resources

* [Steps for installing VASP.5.X.X](https://www.vasp.at/wiki/index.php/Installing\_VASP.5.X.X)
* [Steps for installing VASP.6.X.X](https://www.vasp.at/wiki/index.php/Installing\_VASP.6.X.X)
* [Different type of makefile.include files](https://www.vasp.at/wiki/index.php/Makefile.include)
