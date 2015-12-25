---
layout: post
comments: true
title: "Weird things in programming"
excerpt: "Weird? or not weird?"
date: 2012-12-19 11:00:00
mathjax: true
---

Weird things often occur in programming. We can tell so-called reasons to some of them. For others, the only thing we can do is, how the hell could these be? We have to pay extra attention to avoid these in order not to fall into debugging nightmares.

##10000000000000000 != 10000000000000000 ???
```cpp
float a = 10000000000000000.0;
float b = a - 10000000000000000.0;
```

When printing them, it turns out:

```cpp
a = 10000000272564224.000000
b = 272564224.000000
```

When viewing them in Watch under Debug, it turns out:

|   Name |  Value | Type  |
|:--------:|:--------:|:--------:|
|   a    |  1.0000000e+016 | 	float	|
|   b    |  <span style="color:red">2.7256422e+008</span> | 	float	|

Reason: discussed in [here](http://stackoverflow.com/questions/18877902/why-is-10000000000000000-10000000000000000).

##215510*10000 != 2155100000 ???
```cpp
UINT64 time1 = 215510*10000;
UINT64 time2 = (UINT64)(215510 * 10000);
```

When printing them or in Watch, it turns out:

```cpp
time1 = 18446744071569684320
time2 = 18446744071569684320
```

We have to use one of the following codes in order to get correct answer:

```cpp
UINT64 time3 = (UINT64)215510 * 10000;
UINT64 time4 = 215510 * (UINT64)10000;
UINT64 time5 = (UINT64)215510 * (UINT64)10000;
```

Reason: discussed in [here](http://stackoverflow.com/questions/20727531/why-is-21551010000-2155100000).

##3 < –1 ???
```cpp
int start = 3;

vector<int> data;
data.push_back(0);
data.push_back(0);

for (int i=start; i<data.size()-start; i++)
    printf("In...\n");
```

When running the above code, it will run `printf("In...\n");` infinitely. Although based on the condition (`3<-1`) of the `for` loop, it should never do this. Weird, huh?

To avoid this, you have to compute the long condition equation first, like:

```cpp
... ...
int end = data.size()-start;
for (int i=start; i<end; i++)
    printf("In...\n");
```

**Reason**: discussed in [here](http://stackoverflow.com/questions/20728649/why-is-31-in-code).
