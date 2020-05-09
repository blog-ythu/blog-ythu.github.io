---
layout: redirect
comments: true
title: "Using Libxml2 library in VC++"
excerpt: ""
date: 2012-06-18 11:00:00
mathjax: true
redirect: https://blog-tommyhu.github.io/2012/06/18/Using-Libxml2-library-in-VC/
---

[Libxml2](http://www.xmlsoft.org/) is the XML C parser and toolkit developed for the Gnome project (but usable outside of the Gnome platform), it is free software available under the MIT License. We can use it to read/write data from XML data file.

Steps to use Libxml2 library is as follows:
1. Download [GnuWin](http://gnuwin32.sourceforge.net/) (note that currently GnuWin is only for win32, for win64, we can simply copy installed files in win32 to win64 system, it did work :)) and intall it to anywhere on your PC.
2. Download Libxml2 and extract it to anywhere on your PC.
3. In VC\++ project:
    - Add path `<Libxml2 root>/include` to VC++ Include Directories.
    - Add path `<GnuWin root>/include` to VC++ Include Directories.
    - Add path `<Libxml2 root>/lib` to VC++ Library Directories.
    - Add path `<GnuWin root>/lib` to VC++ Library Directories.
    - Add `libiconv.lib` and `libxml2.lib` to Project Linker Input Additional Dependencies.
    - Copy `libxml2.dll` under path `<Libxml2 root>/bin` to project folder.
    - Copy [`iconv.dll`](https://bitbucket.org/herohuyongtao/using-libxml2-library-in-vs/src/tip/src/Opencv2.3.1/iconv.dll) and [`zlib1.dll`](https://bitbucket.org/herohuyongtao/using-libxml2-library-in-vs/src/tip/src/Opencv2.3.1/zlib1.dll) to project folder.
4. Do like [project](https://bitbucket.org/herohuyongtao/using-libxml2-library-in-vs) to read/write data from/to XML files.
