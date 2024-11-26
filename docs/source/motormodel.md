# User Guide for SCOREC Motor Model

This guide explains in a step-by-step approach the recommended way to install the MotorModel software package.

The simulations within the MotorModel package use MISO, which is a C++ software library built on MFEM. Before continuing, MISO must be completely built, as well as MFEM and other dependencies.

Go to [Insert Link] to find a guide on setting up MISO. It is strongly recommended that everything listed within this guide is built in one folder so that the eventual Python environment will be able to find all of the installed dependencies.

Some of the tests contained within MotorModel require the use of SNOPT, which, unlike the other dependencies for this package, is not open source, and must be purchased. See the [SNOPT Website](http://www.sbsi-sol-optimize.com/asp/sol_product_snopt.htm) for more information.

## Python

While the supporting MFEM and MISO libraries are made in C++, the code that is used to run an analysis is written in Python. Any work with Python must come after all of the above dependencies are installed.

Python must be installed as a systems package. Another package, patch, is also required to install pyOptSparse. Patch is included with WSL [confirm information], but may need to be manually installed if you are using another platform.

```
sudo apt-get install python
sudo apt-get install patch
```

## Environment Setup

A virtual environment must be made to be able to access Python libraries while in the terminal.

```
python -m venv source /[path_to_motor_folder]/python
source /[path_to_motor_folder]/python/bin/activate
```

## Dependency Downloads and Installations

MotorModel, mphys, and omESP must all be downloaded and installed in the Python environment, and MISO must have its Python layer installed as well.

Enter the terminal and navigate to the motor folder. Use the following lines of code to download and install the various Python-based dependencies.

```
cd MISO
pip install -e .
cd ../Dependencies

git clone https://github.com/tuckerbabcock/MotorModel.git
cd MotorModel
git checkout 12d953921a4dc926ac6ef31018de61f35b664014
pip install -e .
cd ..

git clone https://github.com/tuckerbabcock/mphys.git
cd mphys
git checkout c67212caca8ecb4a934b4b4bb05c1989b7041f03
pip install -e .
cd ..

git clone https://github.com/tuckerbabcock/omESP.git
cd omESP
git checkout b421974a6932780f5b80096228cb2bcf0d63c931
pip install -e .
cd ../..
```

## Building PyOptSparse

PyOptSparse is the final installation for Python.

```
git clone https://github.com/OpenMDAO/build_pyoptsparse.git
cd build_pyoptsparse/
pip install -e .
python build_pyoptsparse.py -s '/[path_to_motor_folder]/SNOPT'
cd ..
```

## Running an Analysis
