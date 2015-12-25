---
layout: post
comments: true
title: "Debugging: errors and fixes"
excerpt: "Popular debugging error and fixes."
date: 2012-09-06 11:00:00
mathjax: true
---

[TOC]

## Can not find dxtrans.h in DirectX programming
cc: http://blog.csdn.net/clever101/article/details/5617982

1. Remove `#include "dxtrans.h"` where causing errors (e.g. `qedit.h`).
2. Add the following macro definition to the beginning of code `#include <qedit.h>`

    ```cpp
    #define __IDxtCompositor_INTERFACE_DEFINED__
    #define __IDxtAlphaSetter_INTERFACE_DEFINED__
    #define __IDxtJpeg_INTERFACE_DEFINED__
    #define __IDxtKey_INTERFACE_DEFINED__
    ```

## error C2143: syntax error : missing ')' before 'constant'
E.g. after defining a constant: `#define A 1`, but in program, we mistaken using `int A;` and so on.

Solve: use `A` directly, not `int A`.

## error C2471: cannot update program database ---vc90.pdb
`Project –> Properties –> C/C++ –> General –> Debug Information Format –> 'Disabled'`.

## error C2664: 'CWnd::MessageBoxW' : cannot convert parameter 1 from 'const char [12]' to 'LPCTSTR'
Two ways to fix it:
1. `Project –> Properties –> General –> Character Set –> "Use Multi-Byte Character Set"`.
2. Indicate that the string literal, in this case `"Hello world!"` is of a specific encoding. This can be done through either prefixing it with `L`, such as `L"Hello world!"`, or surrounding it with the generic `_T("Hello world!")` macro. The latter will expand to the L prefix if you are compiling for unicode, and nothing (indicating multi-byte) otherwise.

## error C2665: 'std::_Copy_impl' : none of the 2 overloads could convert all the argument types (xutility)
Based on: https://svn.boost.org/trac/boost/ticket/4874
This is a bug of boost library (before version 1_52_0) when using VC2010 in Debug mode (note: the problem also exists in boost-1_52_0 for VC2012 in Debug mode, but still cannot be solved with this method). It can be solved by modifying `BOOST_PATH/boost/multi_array/view.hpp` to something like

```cpp
multi_array_view& operator=(const multi_array_view& other) {
    if (&other != this) {
        // make sure the dimensions agree
        BOOST_ASSERT(other.num_dimensions() == this->num_dimensions());
        BOOST_ASSERT(std::equal(other.shape(),
            other.shape()+this->num_dimensions(),
            this->shape()));

#if _MSC_VER >= 1600
        auto
            iterThis = begin();
        auto
            iterOther = other.begin();
        for (; iterThis != end(); ++iterThis, ++iterOther)
            *iterThis = *iterOther;
#else
        // iterator-based copy
        std::copy(other.begin(),other.end(),begin());
#endif
    }
    return *this;
}
```

## Error Code: S1023 when installing DirectX SDK (June 2010)
cc: http://blogs.msdn.com/b/chuckw/archive/2011/12/09/known-issue-directx-sdk-june-2010-setup-and-the-s1023-error.aspx
1. Remove the **Visual C++ 2010 Redistributable Package version 10.0.40219 (Service Pack 1)** from the system (both x86 and x64 if applicable). This can be easily done via a command-line with administrator rights:

    ```bat
    MsiExec.exe /passive /X{F0C3E5D1-1ADE-321E-8167-68EF0DE699A5}
    MsiExec.exe /passive /X{1D8E6291-B0D5-35EC-8441-6616F567A0F7}
    ```
2. Install the DirectX SDK (June 2010).
3. Reinstall the **Visual C++ 2010 Redistributable Package version 10.0.40219 (Service Pack 1)**. On an x64 system, you should install both the x86 and x64 versions of the C++ REDIST. Be sure to install the most [current version available](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=26999).

## error LNK1000: Internal error during IncrBuildImage
This is common in VS2008, VS2010 fix this. Two ways to fix it:

1. Install patch [VS90-KB948127.exe](https://bitbucket.org/herohuyongtao/files/src/tip/files/exe/VS90-KB948127.exe).   
2. Two steps:
    1. `Project –> Properties –> Linker –> General –> Enable Incremental Linking –> 'No'`.
    2. `Project –> Properties –> C/C++ –> General –> Debug Information Format –> 'Program Database (/Zi)'`.

## error LNK1104: cannot open file 'cxcore.lib'
`Project –> Properties –> Configuration properties –> Linker – > Input –> Additional Dependence –>` Check whether all libs here are consistent with current PC (e.g. after installing OpenCV2.0, should be cxcore200.lib).

## error LNK2001: unresolved external symbol `__imp___CrtDbgReportW`
This will happen when compiling code under **vc10 + Debug**.

Solve: `Project –> Properties –> C/C++ –> Code Generation –> Runtime Library –> Multi-threaded Debug DLL (/MDd)'`.

## error LNK2001: unresolved external symbol
Reason: not use `class::` before the types/names we defined ourselves.

Solve: try to add these all `class::` needed.

## This source file has changed. It no longer matches the version of the file used to build the application being debugged
1. Check if code contains illegal characters, e.g. copied from QQ.
2. Build your project before you debug.

## Visual Studio Bug: This platform could not be created because a solution platform of the same name already exists
This will happen when you want to create a solution platform for a single project in a big solution, which one of other projects in the solution has already has this solution platform.
Solve [[1]](http://www.dpvreony.co.uk/blog/post/v/62):

1. Close Visual Studio
2. Find the project's (the one that you want to create platform) `.vcproject`  file and open it with e.g. notepad… other than VS.
3. Inside the `<ItemGroup Label="ProjectConfigurations">` node you will find a series of Configuration nodes. Copy (and paste underneath) one which has a name attribute beginning with `Debug|` and replace the text at the end with your desired platform (i.e. Change `Debug|Win32` to `Debug|x64`).
4. Repeat the above step for a `Release` configuration.

---
## Others:
### Error code: S1023 when trying to install DirectX
Solve [[1]](http://answers.microsoft.com/en-us/windows/forum/windows_7-windows_programs/error-code-s1023-when-trying-to-install-directx/0aadf7ec-e004-42eb-8a1a-2865ff5b3a37):

1. Uninstall **Microsoft Visual C++ 2010 x86 Redistributable** and **Microsoft Visual C++ 2010 x64 Redistributable**.
2. Install DirectX again. Should be OK.
3. Install back **Microsoft Visual C++ 2010 x86 Redistributable** and **Microsoft Visual C++ 2010 x64 Redistributable**.
