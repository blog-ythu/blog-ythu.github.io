---
layout: post
comments: true
title: "Conversion between image and world coordinates"
excerpt: "Image coordinates <-> world coordinates."
date: 2012-04-06 11:00:00
mathjax: true
---

Conversion between Image Coordinates and World Coordinates are fundamental to all image formation problems. Always confused to me. Sumup here for further references.

![](https://bytebucket.org/herohuyongtao/blog-files/raw/tip/images/image2.png)

<!-- add TOC here -->
<div id="genTocHere"></div>

## Preknowledge: Camera calibration information

First we have to know camera calibration information, which is essential for these conversions. These parameters contains (see PETS 2009’s calibration data as an example):

**1. Geometry parameters**
- \\(w\\), \\(h\\) (image’s width and height)
- \\(dpx\\), \\(dpy\\)
- \\(dx\\), \\(dy\\)
- \\(ncx\\)
- \\(nfx\\)

**2. Intrinsic parameters**
- \\(f\\) (focal length)
- \\(sx\\)
- \\(cx\\), \\(cy\\)
- \\(kappa1\\)

**3. Extrinsic parameters**
- \\(rx\\), \\(ry\\), \\(rz\\)
- \\(tx\\), \\(ty\\), \\(tz\\)

Based on above parameters, we can get:

1. **Camera transform matrix \\(Pc\\) (3×4)** that multiplies a scene point expressed as a homogeneous coordinate in \\(R^4\\) (\\(w1(x,y,z,1)\\)) to produce an image point expressed as a homogeneous coordinate in \\(R^3\\) (\\(w2(u,v,1)\\)).

	NOTE: Traditionally, we can obtain (u,v) by the following formula that is projected from 3D scene point (x,y,z). But we have to concern image distortion problem, thus, in practice, we often do this by 4 steps as shown in following **PART: World coordinates to image coordinates**.

	$$ w \begin{pmatrix} u \\\ v \\\ 1 \end{pmatrix} = Pc \cdot \lambda \begin{pmatrix} x \\\ y \\\ z \\\ 1 \end{pmatrix} = \begin{pmatrix} R11 & R12 & R13 & R14 \\\  R21 & R22 & R23 & R24 \\\  R31 & R32 & R33 & R34 \end{pmatrix} \cdot \lambda \begin{pmatrix} x \\\ y \\\ z \\\ 1 \end{pmatrix} $$

	![](https://bytebucket.org/herohuyongtao/blog-files/raw/tip/images/image5.png)

	, where

	$$ \begin{cases} \begin{array}{l l} R11 &= cb \cdot cg \\\ R12 &= cg \cdot sa \cdot sb - ca \cdot sg \\\ R13 &= sa \cdot sg + ca \cdot cg \cdot sb  \end{array} \end{cases} $$

	![](https://bytebucket.org/herohuyongtao/blog-files/raw/tip/images/image8.png)

2. **Camera position (\\(C_{posx}\\), \\(C_{posy}\\), \\(C_{posz}\\)):**

	![](https://bytebucket.org/herohuyongtao/blog-files/raw/tip/images/image9.png)

## World coordinates to image coordinates (\\(3D=>2D\\), i.e., \\((x,y,z)=>(u,v)\\))

Meaning: 3D scene point \\((x,y,z) =>\\) 2D image pixel \\((u,v)\\). There need to be 4 steps to do this:

1. **Convert from world coordinates to camera coordinates (\\(3D => 3D\\), i.e., \\((x,y,z)=>(xc,yc,zc)\\)):**
![](https://bytebucket.org/herohuyongtao/blog-files/raw/tip/images/image6.png)
2. **Convert from camera coordinates to undistorted sensor plane coordinates (\\(3D=>2D\\), i.e., \\((xc,yc,zc)=>(xu,yu)\\)):**

	<img src="https://bytebucket.org/herohuyongtao/blog-files/raw/tip/images/image71.png" style="width: 150px;"/>
3. **Convert from undistorted to distorted sensor plane coordinates (\\(2D=>2D\\), i.e., \\((xu,yu)=>(xd,yd)\\))**, reference to the following function in PETS 2009’s calibration pragram:

	```cpp
	void CameraModel::undistortedToDistortedSensorCoord (double Xu, double Yu, double& Xd, double& Yd)
	```
4. **Convert from distorted sensor plane coordinates to image coordinates (\\(2D=>2D\\), i.e., \\((xd,yd)=>(u,v)\\)):**

	<img src="https://bytebucket.org/herohuyongtao/blog-files/raw/tip/images/image72.png" style="width: 180px;"/>

## Image coordinates to world coordinates (\\(2D=>3D\\), i.e., \\((u,v)=>(x,y,z)\\))

*NOTE: If we only have one view, we actually can not get this done unless we also know its z-axis. In this case, please refer to the following function in PETS 2009’s calibration pragram:*
```cpp
bool CameraModel::imageToWorld(double Xi, double Yi, double Zw, double& Xw, double& Yw)
```

Or we can use multi-views to make this done. The following are the steps given two image coordinates from two different views \\((u1,v1)(u2,v2)\\), both of them are projected from the same 3D scene point \\((x,y,z)\\). **In this case, the problem has changed to be 2 \\(2D\\) pair points (corresponding points) \\(=> 3D\\), i.e., \\((u1,v1)(u2,v2)=>(x,y,z)\\).**

The idea is that we anti-project to the \\(3D\\) space (See following figure, \\(C1,C2\\) are the camera positions and \\(p1=(u1,v1)\\), \\(p2=(u2,v2))\\). We based on line \\(R1=C1p1\\) and line \\(R2=C2p2\\). Perfectly, the intersection of \\(R1\\) and \\(R2\\) is what we want \\(P(x,y,z)\\). Unfortunately, \\(R1\\) and \\(R2\\) do not in practice intersect, then the idea changes to find midpoint of shortest perpendicular. The following are the steps:
<img src="https://bytebucket.org/herohuyongtao/blog-files/raw/tip/images/image55.png" style="width: 400px;"/>

1. **Convert from image coordinates to distorted sensor coordinates (\\(2D=>2D\\), i.e., \\((u1,v1)=>(xd1,yd1), (u2,v2)=>(xd2,yd2)\\)):**

	<img src="https://bytebucket.org/herohuyongtao/blog-files/raw/tip/images/image73.png" style="width: 250px;"/>
2. **Convert from distorted sensor to undistorted sensor plane coordinates (\\(2D=>2D\\), i.e., \\((xd1,yd1)=>(xu1,yu1), (xd2,yd2)=>(xu2,yu2)\\)):**

	<img src="https://bytebucket.org/herohuyongtao/blog-files/raw/tip/images/image77.png" style="width: 450px;"/>
3. **Convert from undistorted sensor plane coordinates to camera coordinates (\\(2D=>3D\\), i.e., \\((xu1,yu1)=>(xc1,yc1,zc1), (xu1,yu1)=>(xc1,yc1,zc1)\\)):**

	<img src="https://bytebucket.org/herohuyongtao/blog-files/raw/tip/images/image78.png" style="width: 120px;"/>

	NOTE: Here assumed that image plane are between camera position and the scene, if are not, \\(zc\\) should be set to be \\(–f\\).
4. **Convert from camera coordinates to world coordinates (\\(3D=>3D\\), i.e., \\((xc1,yc1,zc1)=>(x1,y1,z1), (xc2,yc2,zc2)=>(x2,y2,z2)\\)):** refer to the following function in PETS 2009’s calibration pragram:

	```cpp
	bool CameraModel::cameraToWorldCoord (double xc, double yc, double zc, double& xw, double& yw, double& zw)
	```
5. Compute closest pair points of pair camera rays, say M1M2.
6. Return M1M2’s midpoint as result.

## References
1. Multi-Camera Networks: Principles and Applications, H. Aghajan, A. Cavallaro, Elsevier, ISBN: 978-0-12-374633-7, May 2009.
2. PETS 2009 Benchmark Data, http://www.cvg.rdg.ac.uk/PETS2009/a.html.
