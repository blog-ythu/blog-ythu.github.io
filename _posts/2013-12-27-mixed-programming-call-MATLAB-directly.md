---
layout: post
comments: true
title: "MATLAB/C++ Mixed Programming: call MATLAB directly in VC++"
excerpt: "Another method for MATLAB/C++ Mixed Programming by calling MATLAB directly in VC++."
date: 2013-12-27 11:00:00
mathjax: true
---

There are two ways in VC++ to call MATLAB:

1. distribute MATLAB code into MATLAB independent C++ shared library. Refer to the [previous blog](http://blog-ythu.github.io/2012/04/29/mixed-programming-distribute-MATLAB/) for details.

2. call MATLAB directly in VC++.

---
This blog will focus on the second approach (also the easier one).

<!-- add TOC here -->
<div id="genTocHere"></div>

## Pre-work:
- Install MATLAB: unlike the first method, make sure you have the whole MATLAB application installed in order to call MATLAB directly in VC++ (only MATLAB Compiler Runtime is needed for the first method). We will take **MATLAB-x64-R2013a** as an example (installed under `C:\Program Files\MATLAB\R2013a`).
- **Platform consistence:** ***the platforms of MATLAB and VC++ compile platform must be the same***, i.e. Win32/x86 VC\++ compile platform can only use x86 MATLAB and x64 VC\++ compile platform can only use x64 MATLAB.
- Example: take the following example `myadd2.m` (assume under `C:\`):

	```matlab
	function [y, z] = myadd2(a, b)
	% dummy function, just to demonstrate the idea
	y = a+b;
	z = a+2*b;
	disp('Output from MATLAB.');
	end
	```

## VC setup:
1. Add MATLAB include folder to project `Include` files: i.e. `C:\Program Files\MATLAB\R2013a\extern\include`
2. Add MATLAB library folder to project `Library` files: i.e. `C:\Program Files\MATLAB\R2013a\extern\lib\win64\microsoft`
3. Add relevant libraries to `Linker>Input>Additional Dependencies`: `libmx.lib libeng.lib`
4. Add MATLAB Compiler Runtime binary folder to project `Debugging>Environment`: i.e. `PATH=%PATH%;$(ProjectDir)\dlls_x64;C:\Program Files\MATLAB\R2013a\runtime\win64;C:\Program Files\MATLAB\R2013a\bin\win64`
5. Add header to main code: `#include "engine.h"`
2. Call in VC++: refer to [`VC_call_MATLAB_directly.cpp`](https://bitbucket.org/herohuyongtao/files/src/tip/files/cplusplus/VC_call_MATLAB_directly.cpp) for details.

	In this cpp file, there are also ways to handle data passing of different types between VC++ and MATLAB (see the `SHOW_EXTEND_INFO` part), which includes:
    - `double` (or `int`/`float`/…)
    - `string`
    - matrix
    - gray-scale image
    - RGB image

## Other Remarks:
1. If `engOpen(NULL)` always returns `0` or `NULL`, you should register MATLAB first by[^1]
	- Run DOS in admin mode.
	- In DOS, go to MATLAB install folder that contains `matlab.exe`, e.g. `D:\Program Files\MATLAB\R2014b\bin`.
	- Run command: `./matlab.exe /regserver`.

	> Note: it seems that given MATLAB can not both register both x86 & x64 (e.g. can only register MATLAB-2015b-x86 or MATLAB-2015b-x64, but not both). On the other hand, if we do want both x86 and x64 modes, we can install one MATLAB for one mode and one for another, e.g. MATLAB-2015b-x86 and MATLAB-2014b-x64.

2. Try to close the previous MATLAB window first (extra command window) if stuck at `engOpen(NULL)`.

[^1]: `engOpen()` always returns 0: http://www.mathworks.com/matlabcentral/newsreader/view_thread/238828
