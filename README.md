# Polylidar with Intel RealSense Example - Ground/Obstacle Detection

This repository contains code and examples for integrating [Polylidar](https://github.com/JeremyBYU/polylidarv2) with an Intel RealSense camera.  The examples presented is for ground/obstacle detection with representation as polygons. 

![test](assets/media/obstacles_walk.gif)

The main components of the code are as follows:
1. Using `pyrealsense2` to interface with the a D435 sensor.
2. Apply filtering to generated depth images (spatial,temporal, etc.).  
3. Generate a point cloud from filtered depth image.
4. Find ground normal and rotate point cloud to align its z-axis with ground normal.
5. Use `polylidar` to extract flat surfaces and obstacles as polygons
6. Perform polygon filtering and buffering.
7. Project polygons onto image for display and verification


Please see disclaimers below before using Polylidar with an Intel RealSense camera for your application.

## Installation

1. Install [conda](https://conda.io/projects/conda/en/latest/) - [Why?](https://medium.freecodecamp.org/why-you-need-python-environments-and-how-to-manage-them-with-conda-85f155f4353c)
2. `conda create --name realsense python=3.6 && source activate realsense` - Create new virtual python environment
3. `git clone --recurse-submodules https://github.com/JeremyBYU/polylidar-realsense.git && cd polylidar-realsense`
4. `conda install -c conda-forge opencv shapely pybind11` - These packages give the most issue for binary dependencies for Windows users, hence why conda should handle them.
5. `cd thirdparty/polylidar && pip install -e . && cd ../..` - Install polylidar manually because it is not on PyPi.
6. `pip install -e .` - Install any dependencies for this repository (groundetector).


## Running

The program which runs the capture is `capture.py` and be called as so `python grounddetector/capture.py`.  All parameters used to configure the intel RealSense camera can be found in `grounddetector/config/default.yaml`. You can specify alternate configuration profiles as a command line parameter:

```
usage: capture.py [-h] [-c CONFIG]

Captures ground and obstacles using polylidar

optional arguments:
  -h, --help            show this help message and exit
  -c CONFIG, --config CONFIG
                        Configuration file
```

## Disclaimers

The Intel D435 is very noisy with very dense point clouds. The only way to make it usable for Polylidar (as it is currently implemented) is to use heavy filtering, including temporal filtering. Also the points are downsampled to:

1. Increase runtime speed. Less points the faster polylidar can extract polygons.
2. Create more gaps between points, to increase triangle size. The true planarity (normal) of a triangle is more apparent the larger the triangle is in relation to sensor noise. 

Imagine a ground triangle with 1cm edge lengths with a height noise of 1 cm. The noise dominates the triangle and makes the normal of the triangle not planar. Now image 5 cm edge lengths with same 1cm height noise. The noise makes less of a difference and it appears to be more flat.