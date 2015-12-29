---
layout: post
comments: true
title: "Running Python in Windows and install external libraries"
excerpt: ""
date: 2013-10-05 11:00:00
mathjax: true
---

<!-- add TOC here -->
<div id="genTocHere"></div>

## Setup Python environment in Windows: (using [Anaconda](http://continuum.io/downloads))
1. Download Window's version [Anaconda](http://continuum.io/downloads).
2. Install (e.g. default to folder `C:\Anaconda`).
3. Setup python system variable: new a system variable with name `PYTHONPATH` and value `C:\Anaconda\Lib\site-packages`.

## Install external libraries (3 different ways):
1. **Install from Anaconda server:**
 - Run **Anaconda Command Prompt** under Anaconda's start menu.
 - To install **py** library, type `conda install py` (You can first type `conda search xxx` to search **xxx** library exist in Anaconda server or not, full list is available by type `conda list`).
2. **Install from external libraries directly:** (Take [**protobuf**](https://code.google.com/p/protobuf/) as an example in `C:\Libs\protobuf-2.4.1`)
 - Run **Anaconda Command Prompt** under Anaconda's start menu.
 - cd to protobuf's folder: `C:\Libs\protobuf-2.4.1\python`.
 - Build: `python setup.py build`
 - Install: `python setup.py install`
 - Done. Then you can e.g. `from google.protobuf import descriptor` and use e.g. `descriptor.FileDescriptor()` in **.py** source code.
3. **Install by copy files directly:** (Take **OpenCV** as am example in `C:\Libs\OpenCV-2.3.1`)
 - Copy **cy.py** and **cv2.pyd** from folder `C:\Libs\OpenCV-2.3.1\build\python\2.7` to `C:\Anaconda\Lib\site-packages`.
 - Done. Then you can `import cv/cv2` and use e.g. `cv2.imwrite()` in **.py** source code.

## Run Python
1. Run **Anaconda Command Prompt** under Anaconda's start menu.
2. Change to the folder that contains the Python file that you want (assume **xxx.py**) to run.
3. Run: `> python xxx.py`.
