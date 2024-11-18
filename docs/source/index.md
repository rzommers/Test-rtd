# User Guide for MISO



## Windows Subsystem for Linux (WSL)

A Linux OS must be used to access the provided tools. If you are already running on a Linux OS (Ubuntu, Fedora, etc.), you should skip this test. If you are running on a Windows OS, one available option to access this content is by using a Linux simulator; for example, WSL.

## OpenMPI (or alternative)

OpenMPI is a software package that is an implementation of the Message Passing Interface (MPI) standard that is required for a parallel build of MFEM. Alternatives exist, such as MPICH, but OpenMPI is the recommended MPI implementation.

## File Organization

For late-stage integration to work properly, all files should be downloaded and installed in one singular folder.

## MFEM Prerequisites

Two dependencies are required in order to build the MFEM library, used for finite element discretization: HYPRE and METIS.

### HYPRE

```
cd Dependencies/hypre/src
./configure
cd cmbuild
cmake .. -DCMAKE_POSITION_INDEPENDENT_CODE=ON -DHYPRE_INSTALL_PREFIX="[path_to_motor_folder]/Installations/hypre" -DHYPRE_ENABLE_SHARED=ON
make install
cd ../../..
export PATH=$PATH:[path_to_motor_folder]/Installations/hypre
```

### METIS

```
cd Dependencies/metis
make config shared=1 cc=gcc prefix=[path_to_motor_folder]/Installations/metis
make -j 4
make install
cd ../..
export PATH=$PATH:[path_to_motor_folder]/Installations/metis
```

## PUMI (additional mesh tool)

PUMI is a library that is optional for standalone MFEM, but is required for certain MISO tests. This project uses the CORE package, which is a modification of PUMI and serves the same purpose. EngineeringSketchPad (ESP) is a dependency for CORE, and OpenCASCADE is in turn a requirement for ESP.

### OpenCASCADE



### ESP

```
cd Dependencies
git clone https://github.com/tuckerbabcock/EngSketchPad.git
cd EngSketchPad
git checkout 8a5ca2bd6fbed747d371eb926e8a24d0cfcda087
cd config
./makeEnv /[path_to_motor_folder]/Installations/OpenCASCADE
cd ..
source ESPenv.sh
cd src
make
cd ../../..
```

### CORE (PUMI)

#### config_core.sh

```
cmake .. \
  -DCMAKE_C_COMPILER="MPICC" \
  -DCMAKE_CXX_COMPILER="MPICXX" \
  -DCMAKE_INSTALL_PREFIX="/[path_to_motor_folder]/Installations/core \
  -DBUILD_SHARED_LIBS=ON \
  -DSCOREC_CXX_OPTIMIZE=ON \
  -DSCOREC_CXX_SYMBOLS=ON \
  -DSCOREC_CXX_WARNINGS=OFF \
  -DSCOREC_EXTRA_CXX_FLAGS="-Wextra -Wall" \
  -DENABLE_ZOLTAN=OFF \
  -DENABLE_EGADS=ON \
  -DPUMI_USE_EGADSLITE=OFF \
  -DIS_TESTING=OFF \
```

```
cd Dependencies
git clone https://github.com/tuckerbabcock/core.git
cd core
git checkout egads-dev
mkdir build
cd build
source config_core.sh
make -j 4
make install
cd ../../..
export PATH=$PATH:/[path_to_motor_folder]/Installations/core
```

## Building MFEM

#### config_mfem.sh

```
cmake .. \
  -DCMAKE_CXX_COMPILER="MPICXX" \
  -DCMAKE_BUILD_TYPE=Debug \
  -DCMAKE_INSTALL_PREFIX="/[path_to_motor_folder]/Installations/mfem" \
  -DBUILD_SHARED_LIBS=ON \
  -DMFEM_DEBUG=ON \
  -DMFEM_USE_MPI=ON \
  -DMFEM_USE_METIS=ON \
  -DMFEM_USE_METIS_5=ON \
  -DMFEM_USE_PUMI=ON \
  -DPUMI_DIR="/[path_to_motor_folder]/Installations/core" \
  -DMFEM_ENABLE_EXAMPLES=OFF \
  -DMFEM_ENABLE_MINIAPPS=OFF \
```

```
cd Dependencies
git clone https://github.com/mfem/mfem.git
cd mfem
git checkout odl
mkdir build
cd build
source config_mfem.sh
make -j 4
make install
make examples -j 4
make test
cd ../../..
export PATH=$PATH:/[path_to_motor_folder]/Installations/mfem
```

## MISO

MISO is the main library used to make finite element simulations, and is based on MFEM. One additional dependency, ADEPT, used for automatic differentiation, is required before the build process can begin.

### ADEPT

```
cd Dependencies/adept
./configure --prefix="/[path_to_motor_folder]/Installations/adept
make
make install
cd ../..
export PATH=$PATH:/[path_to_motor_folder]/Installations/adept
```

### Building MISO

#### config_miso.sh

```
cmake .. \
  -DCMAKE_C_COMPILER="MPICC" \
  -DCMAKE_CXX_COMPILER="MPICXX" \
  -DCMAKE_BUILD_TYPE=Debug \
  -DMFEM_DIR="/[path_to_motor_folder]/Dependencies/mfem" \
  -DAdept_ROOT="/[path_to_motor_folder]/Installations/adept" \
  -DPUMI_DIR="/[path_to_motor_folder]/Installations/core" \
  -DBUILD_SHARED_LIBS=OFF \
  -DBUILD_TESTING=ON \
  -DCMAKE_EXPORT_COMPILE_COMMANDS=ON \
```

```
git clone https://github.com/OptimalDesignLab/MISO.git
cd MISO
git checkout dev
mkdir build
cd build
source config_miso.sh
make -j 4
make install
make tests
cd ../..
```

## Python

While the supporting MFEM and MISO libraries are made C++, the code that is used to run analyses is written in Python. Any work with Python must come after all of the above dependencies are installed.

### Environment Setup

A virtual environment must be made to be able to access Python libraries while in the terminal.

### Dependency Downloads

The following Python libraries must be downloaded: MotorModel, mphys, omESP, and build_pyoptsparse.

### Dependency Installations

There are a number of Python installations that must be done, but it is important that the order in which they are installed is in a certain way. The below instructions follow the exact order.

### Building PyOptSparse

PyOptSparse is the final installation for Python

## Running the Analysis
