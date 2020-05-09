---
layout: redirect
comments: true
title: "Track memory leaks in VS"
excerpt: "Memory is never enough if facing memory leaks."
date: 2015-06-23 11:00:00
mathjax: true
redirect: https://blog-tommyhu.github.io/2015/06/23/Track-memory-leaks-in-VS/
---

In VS, memory leaks can be detected by using C Run-Time (CRT) libraries[^1]. A typical example of enabling this feature can be like:

```cpp
/* the #include statements must follow the order shown here */
#define _CRTDBG_MAP_ALLOC // show file name and line number when detecting leaked memory
#include <stdlib.h>
#include <crtdbg.h>

/* make it work also for c++ new operator, together with malloc */
#ifdef _DEBUG
#ifndef DBG_NEW
#define DBG_NEW new ( _NORMAL_BLOCK , __FILE__ , __LINE__ )
#define new DBG_NEW
#endif
#endif  // _DEBUG

int main()
{
	/* these two lines will report all memory leaks after application exits */
	_CrtSetDbgFlag(_CRTDBG_ALLOC_MEM_DF | _CRTDBG_LEAK_CHECK_DF);	// turn on leak checking flag
	_CrtSetReportMode(_CRT_ERROR, _CRTDBG_MODE_DEBUG);				// set report to the Output window

	/* set startint point that want to check memory leak */
	_CrtMemState s1, s2, s3;
	_CrtMemCheckpoint(&s1);

	/* ---your real code goes here--- */
	{
		int *test = new int[10];					// line 26
		int *test2 = new int[20];					// line 27
		int *test3;
		test3 = (int *)malloc(sizeof(int) * 30);	// line 29
	}

	/* set ending point that want to check memory leak */
	_CrtMemCheckpoint(&s2);
	if (_CrtMemDifference(&s3, &s1, &s2)){ // only report memory leak if happens indeed
		_CrtMemDumpStatistics(&s3);
	}

	return 0;
}
```

This will report the memory leaks in the **Output Window** as follows:

```cpp
0 bytes in 0 Free Blocks.
240 bytes in 3 Normal Blocks.
0 bytes in 0 CRT Blocks.
0 bytes in 0 Ignore Blocks.
0 bytes in 0 Client Blocks.
Largest number used: 240 bytes.
Total allocations: 240 bytes.
-----------------------------------
Detected memory leaks!
Dumping objects ->
..\test\main.cpp(29) : {415} normal block at 0x00A8FB60, 120 bytes long.
 Data: <                > CD CD CD CD CD CD CD CD CD CD CD CD CD CD CD CD
..\test\main.cpp(27) : {414} normal block at 0x00A8FAD0, 80 bytes long.
 Data: <                > CD CD CD CD CD CD CD CD CD CD CD CD CD CD CD CD
..\test\main.cpp(26) : {413} normal block at 0x00A8FA68, 40 bytes long.
 Data: <                > CD CD CD CD CD CD CD CD CD CD CD CD CD CD CD CD
Object dump complete.
```

Based on the memory leak report, we can easily identify where memory leaks happens (in this case, **line 26/27/29**) and how bad they are (e.g. **leaking 120 bytes in line 29**).

Note that:
1. **The second part of the memory leak report that contains file names and line numbers is only available after the whole application exits**.
2. To track memory leaks before application exit, we have to set starting and ending points in code, as we did in the above example. By moving the starting and ending points around, it is flexible for us to track the exact code that we're focused on. Sample memory leak report can be seen from the first part of above report.

---
[^1]: Finding Memory Leaks Using the CRT Library: https://msdn.microsoft.com/en-us/library/x98tx3cf.aspx
