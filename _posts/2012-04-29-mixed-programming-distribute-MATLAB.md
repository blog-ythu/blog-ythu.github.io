---
layout: post
comments: true
title: "MATLAB/C++ Mixed Programming: distribute MATLAB code into independent C++ shared library"
excerpt: "MATLAB/C++ Mixed Programming by distributing MATLAB code into independent C++ shared library."
date: 2012-04-29 11:00:00
mathjax: true
---

There are two ways in VC++ to call MATLAB:

1. distribute MATLAB code into MATLAB independent C++ shared library.
2. call MATLAB directly in VC++. Refer to the [this blog](http://blog-ythu.github.io/2013/12/27/mixed-programming-call-MATLAB-directly/) for details.

This blog will focus on the first approach.

---
Distributing MATLAB code into C\++ libraries will enable us to call MATLAB in C\++. This is further can be done to be MATLAB independent, which means we can call these libraries on any PC without MATLAB pre-installed. Also, good news is that, according to [MATLAB Support](http://www.mathworks.com/support/solutions/en/data/1-GQC9MB/index.html), all deployed components and applications can be distributed free of charge. So, in this way, we can integrate MATLAB codes into our own product without concerning the license problem.

---
In the following, I will show you how you can do this step by step.

[TOC]

## Pre-work:
Take the following MATLAB function `myadd2.m` as an example, from which we want to generate MATLAB independent C++ shared library.

```matlab
function [y, z] = myadd2(a, b)
% dummy function, just to demonstrate the idea
y = a+b;
z = a+2*b;
end
```

## Deploy in MATLAB:
1. Setup mex in MATLAB if needed, see [MATLAB/C++ Mixed Programming: setup mex in MATLAB](http://herohuyongtao.blogspot.hk/2012/04/matlabc-mex.html).
1. \>> `mcc -C -W cpplib:libmyadd2 -T link:lib myadd2.m` (`-C` option is needed to generate `.ctf` file).
1. Done. We can find the following files generated in the file folder.
    - **libmyadd2.h**
    - **libmyadd2.ctf**
    - **libmyadd2.dll**
    - **libmyadd2.lib**
    - libmyadd2_mcc_component_data.c
    - libmyadd2.cpp
    - libmyadd2.exports
    - libmyadd2.exp
    - libmyadd2.prj

    Among these, only the first 4 will be needed to be called in VC.

NOTE: alternately, we can use MATLAB **deploytool** to do this:

1. \>> `deploytool`
1. Pre-setup: In the **Deployment Project** window, edit project `Name` (also the name for the generated .h/.lib/.dll files, `libmyadd2` here), `Location` and most importantly choose the deployment `Type` to be `C++ shared library` (4 types in total to choose from including `Windows Standalone Application`, `Console Application`, `C Shared Library and C++ Shared Library`; the other 3 are not fully tested).
1. Add files: In the **C++ Shared Library** window, under `Build` menu, click `[Add files]` to add `myadd2.m` as our exported functions.
1. Setup: Click `Actions` Button in the `C++ Shared Library` window `> Settings… > General >` uncheck the `'Embed CTF archive into the Application'`. Note that if we leave this option checked by default, it will not generate `.ctf` file. Their differences are:
 - Without `.ctf`: when called in VC\++, the runtime temp files needed will be generated in system Temp folder, i.e. `C:\Users\tommyhu\AppData\Local\Temp\tommyhu\mcrCache7.17\myadd20`.
 - With `.ctf`: when called in VC\++, the runtime temp files needed will be generated in VC++ solution folder, i.e. `C:\Users\tommyhu\Documents\visual studio 2010\Projects\test\libmyadd2_mcr`.
1. Build: Click **Build** Button in the **C++ Shared Library window** to do real building.
1. Done. Under `..\folderWeSet\libmyadd2\distrib`, we will find all files we need including:
    - **libmyadd2.h**
    - **libmyadd2.ctf**
    - **libmyadd2.dll**
    - **libmyadd2.lib**

## Setup client PC:
Note: skip this step if you just want to call them in the same PC or another PC which has already installed MATLAB of the same version.

- Copy **MCRInstaller.exe** to client PC and install. Note that **MCRInstaller.exe** should be consistent with the MATLAB version. Usually you can find it under path like` …\MATLAB\R2012a\toolbox\compiler\deploy\win64`.

Note: although we have generated MATLAB independent C++ shared libraries, we still need to slightly setup client PC in order to make it work. Actually we still need to install MATLAB Compiler Runtime (**MCRInstaller.exe**) of it, but at least not full of it, make it portable enough. In reality, we can package it together with our product (and install automatically).

## Call in VC++:
3. Libraries preparation: copy the above 4 files into VC project.
3. VC setup:
 - Add MATLAB Compiler Runtime include folder to project `Include files`: i.e. `C:\Program Files\MATLAB\MATLAB Compiler Runtime\v717\extern\include`.
 - Add MATLAB Compiler Runtime library folder to project `Library files`: i.e. `C:\Program Files\MATLAB\MATLAB Compiler Runtime\v717\extern\lib\win64\microsoft`.
 - Add MATLAB Compiler Runtime binary folder to project `Debugging>Environment`: i.e. `PATH=%PATH%;C:\Program Files\MATLAB\MATLAB Compiler Runtime\v717\runtime\win64;C:\Program Files\MATLAB\MATLAB Compiler Runtime\v717\bin\win64`.
 - Add relevant libraries to `Linker>Input>Additional Dependencies`: `libmyadd2.lib mclmcrrt.lib mclmcr.lib`. (Note also to add `libmx.lib` and `libmat.lib` if you plan to use `mxArray` in your code.)
 - Add header to main code: `#include "libmyadd2.h"`.
3. Calling: please refer to [`VC_call_MATLAB.cpp`](https://bitbucket.org/herohuyongtao/files/src/tip/files/cplusplus/VC_call_MATLAB.cpp).

Note that, if you just want to call them in the same PC or another PC which has already installed MATLAB of the same version, you don't need step 2 to install MATLAB Compiler Runtime (**MCRInstaller.exe**) because these have been included when you install MATLAB. Under this condition, instead, you can setup VC as follows:

- VC setup:
 - Add MATLAB include folder to project **Include files**: i.e. `C:\Program Files\MATLAB\R2012a\extern\include`
 - Add MATLAB library folder to project **Library files**: i.e. `C:\Program Files\MATLAB\R2012a\extern\lib\win64\microsoft`
 - Add relevant libraries to **Linker>Input>Additional Dependencies**: `libmyadd2.lib mclmcrrt.lib mclmcr.lib`
 - Add header to main code: `#include "libmyadd2.h"`

---
## Other Remarks:
1. When .m file that we want to distribute also calls several other .m files, we don't need to particularly to indicate. The steps are the same: only the final .m file is need to be distributed.
2. Different distributed shared libraries perform independently, i.e., they cannot share variables to work together. For example, it is impossible for one library to use the data created by another library, which will be released when its library ends.
3. **Thread-safety**: According to [MATLAB documentation](http://www.mathworks.com/help/matlab/matlab_external/using-matlab-engine.html), MATLAB libraries are not thread-safe. If you create multithreaded applications, make sure only one thread accesses the engine application. For example, if you want to use the above `libmyadd2.lib`, use [ThreadLock.h/.cpp](https://bitbucket.org/herohuyongtao/useful-functions) as follows:

    ```cpp
    #include "ThreadLock.h"

    static ThreadLock Lock;
    Lock.Lock();
    myadd2(2, y, z, a, b);
    Lock.Unlock();
    ```

4. **Type passing**: Passing C++ native types to `mwArray` in order to call MATLAB
  - When passing a grayscale `Mat` image, can use `mxINT8_CLASS` as follows.

        ```cpp
        // assume we want to passing Mat img(rows, cols, CV_8UC1)
        int rows = img.rows;
        int cols = img.cols;
        mwArray imgP(rows, cols, mxUINT8_CLASS);

        // remember to tranpose first because MATLAB is col-major
        transpose(mat, mat);
        imgP.SetData(mat.ptr(), rows*cols);
        ```
  - When passing a color `Mat` image (assume in format `CV_RGB`), currently no way is found to pass it directly. Possible way is to pass the r, g, b channels independently and then combine them to be a color image in MATLAB as follows.
	- In VC++:

        ```cpp
        // suppose we want to pass Mat img
        vector<Mat> channels; // R, G, B channels
        split(img, channels);

        int rows = img.rows;
        int cols = img.cols;
        mwArray image_R(rows, cols, mxUINT8_CLASS);
        mwArray image_G(rows, cols, mxUINT8_CLASS);
        mwArray image_B(rows, cols, mxUINT8_CLASS);

        // remember to tranpose first because MATLAB is col-major
        transpose(channels[0], channels[0]);
        transpose(channels[1], channels[1]);
        transpose(channels[2], channels[2]);
        image_R.SetData(channels[0].ptr(), rows*cols);
        image_G.SetData(channels[1].ptr(), rows*cols);
        image_B.SetData(channels[2].ptr(), rows*cols);
        ```
	- In MATLAB:

        ```matlab
        image(:,:,1) = image_R;
        image(:,:,2) = image_G;
        image(:,:,3) = image_B;
        ```
	- The way to define the input parameter based on a `string` or `char []` is as follows:

        ```cpp
        // ini input parameter: im_path based on string aString or char aString[]
        mwArray im_path(aString.c_str());
        ```
	- For passing other types, we had better to use double (`mxDOUBLE_CLASS`) instead of int (`mxINT8_CLASS`) even though it actually is a int.
	- For passing complex values:

        ```cpp
        // passing to MATLAB
        double rdata[4] = {1.0, 2.0, 3.0, 4.0};
        double idata[4] = {10.0, 20.0, 30.0, 40.0};
        mwArray a(2, 2, mxDOUBLE_CLASS, mxCOMPLEX);
        a.Real().SetData(rdata, 4);
        a.Imag().SetData(idata, 4);

        // get back from MATLAB
        double rbuffer[4];
        double ibuffer[4];
        a.Real().GetData(rbuffer,4);
        a.Imag().GetData(ibuffer,4);
        ```
