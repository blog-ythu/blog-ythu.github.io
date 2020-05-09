---
layout: redirect
comments: true
title: "Build Boost for x32/x64 VC++ compilers on Windows"
excerpt: ""
date: 2013-01-02 11:00:00
mathjax: true
redirect: https://blog-tommyhu.github.io/2013/01/02/Build-Boost-for-x32-x64-VC-compilers-on-Windows/
---

[Boost](http://www.boost.org/) is a set of libraries for the C\++ programming language that provide support for tasks and structures such as linear algebra, pseudorandom number generation, multithreading, image processing, regular expressions, and unit testing. Steps to build Boost for x32/x64 VC\++ compilers on Windows are as follows:

1. Download [Boost](http://www.boost.org/) and extract it to PC (assume `C:\Libs\boost_1_47_0`).
2. Double click to run `bootstrap.bat` in Boost's root folder, which will generate `bjam.exe` (and `b2.exe`...) in the same folder.
3. Run **VC\++**.
4. `VC++ > Tools > Visual Studio Command Prompt`.
5. In the command window, first change working folder to `C:\Libs\boost_1_47_0` and type the following command (Note: it will take a long time to finish, which is depended on your system, 20min for me.):

    ```bat
    bjam --build-type=complete toolset=msvc-10.0 threading=multi link=static address-model=64
    ```
6. Setup VC\++. Two steps need to be done.
 - Add `C:\Libs\boost_1_47_0` to project Include files.
 - Add `C:\Libs\boost_1_47_0\stage\lib` to project Library files.

## Remarks:
The above step will build **static Boost libraries for x64 VC++**. Options can be modified in step 5 to build it for other versions' VC\++, see the following table for details.

| Command |	Options |	Meaning |
|:-------:|:-------:|:---------:|
|toolset  | <span style="color:red">msvc-6.5</span> <br> <span style="color:red">msvc-8.0</span> <br> <span style="color:red">msvc-9.0</span> <br> <span style="color:red">msvc-10.0</span> <br> <span style="color:red">msvc-11.0</span> <br> <span style="color:red">msvc-12.0</span> | Visual Studio 6.0, Service Pack 5 <br> Visual Studio 2005 <br> Visual Studio 2008 <br> Visual Studio 2010 <br> Visual Studio 2012 <br> Visual Studio 2013 |
| link	| <span style="color:red">static</span> <br> <span style="color:red">shared</span> | static libraries <br> shared libraries |
| address-model	| <span style="color:red">32</span> <br> <span style="color:red">64</span> |	x32 VC\++ compilers <br> x64 VC\++ compilers |
