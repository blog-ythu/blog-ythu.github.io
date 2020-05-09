---
layout: redirect
comments: true
title: "Research Topics Related Useful Links"
excerpt: "Representative work summary of research topics."
date: 2011-11-15 11:00:00
mathjax: true
redirect: https://blog-tommyhu.github.io/2011/11/15/Research-Topics-Related-Useful-Links/
---

<!-- add TOC here -->
<div id="genTocHere"></div>

## 3D face (facial expressions) databases
- [Bosphorus database (face & hand)](http://bosphorus.ee.boun.edu.tr/)
- [Morphace](http://faces.cs.unibas.ch/bfm/main.php)
- [Analyzing facial expressions in three dimensional space](http://www.cs.binghamton.edu/~lijun/Research/3DFE/3DFE_Analysis.html)
- [Face recognition](http://www.frav.es/research/facerecognition/)
- [CASIA 3D face database](http://www.cbsr.ia.ac.cn/english/3DFace%20Databases.asp)
- [BJUT-3D face database](http://www.bjut.edu.cn/sci/multimedia/mul-lab/3dface/face_database.htm)

## Human detection
- Dataset and ground-truth
	- [PETS 2009 | TUD | EPFL](http://www.milanton.de/data.html)
- Papers:
    - [Piotr Dollár’s work](http://vision.ucsd.edu/~pdollar/research.html#ObjectDetection)
        - 09 BMVC - Integral Channel Features
        - 10 BMVC - The Fastest Pedestrian Detector in the West
        - 12 ECCV - Crosstalk Cascades for Frame-Rate Pedestrian Detection
        - 12 PAMI - Pedestrian Detection: An Evaluation of the State of the Art
        - 14 PAMI - Fast Feature Pyramids for Object Detection
- Other:
	- [12 CVPR - Pedestrian detection at 100 frames per second](http://rodrigob.github.io/)

## Human Pose Estimation
- Dataset and Benchmark
	- [MPII Human Pose Dataset](http://human-pose.mpi-inf.mpg.de/)
- Papers
	- [13 CVPR - Beyond Physical Connections: Tree Models in Human Pose Estimation](http://users.cecs.anu.edu.au/~yili/humanpose.html): with MATLAB code

## Object tracking
- Dataset and ground-truth
	- [PETS 2009 | TUD-Stadtmitte | TUD-Campus | TUD-Crossing | ETH-Person](http://www.milanton.de/data.html)
- Single camera tracking:
    - Online object tracking:
    	- [Visual Tracker Benchmark](http://cvlab.hanyang.ac.kr/tracker_benchmark/index.html)
    	- [Visual Object Tracking (VOT) challenge](http://www.votchallenge.net/)
    	- [13 CVPR - Online Object Tracking: A Benchmark](http://visual-tracking.net/)
    	- [13 ICCV - Tracking Revisited using RGBD Camera: Unified Benchmark and Baselines](http://tracking.cs.princeton.edu/eval.php)

	    > Deep Learning based trackers:
	    > - 15 ICML - Online Tracking by Learning Discriminative Saliency Map with Convolutional Neural Network
	    > - 15 arXiv - Learning Multi-Domain Convolutional Neural Networks for Visual Tracking

    - Tracking-by-detection/data-association:
        - [14 PAMI - Continuous Energy Minimization for Multi-Target Tracking](http://www.milanton.de/contracking): with MATLAB code
        - [14 CVPR - Occlusion Geodesics for Online Multi-Object Tracking](https://lrs.icg.tugraz.at/download#motog): with MATLAB code
        - [12 CVPR - Discrete-Continuous Energy Minimization for Multi-Target Tracking](http://www.milanton.de/dctracking/): with MATLAB code
        - [12 CVPR - Multi-Target Tracking by Online Learning of Non-linear Motion Patterns and Robust Appearance Models](http://iris.usc.edu/Outlines/papers/2012/yang-nevatia-cvpr-1-2012.pdf)
        - [12 AVSS - Online Multi-Person Tracking by Tracker Hierarchy](http://cs-people.bu.edu/jmzhang/tracker_hierarchy/Tracker_Hierarchy.htm): with C++ code
        - [11 CVPR - Globally-Optimal Greedy Algorithms for Tracking a Variable Number of Objects](http://people.csail.mit.edu/hpirsiav/papers/tracking_cvpr11.pdf): with MATLAB code
- Multiple camera tracking:
	- Metrics and ground-truth labeling: [13 CVPRW - Challenges of Ground Truth Evaluation of Multi-Target Tracking](http://www.milanton.de/files/cvprws2013/cvprws2013-anton.pdf)
    - Papers:
        - [Tracking People using Multiple Cameras](http://cvlab.epfl.ch/research/body/surv)
        - [The future of video surveillance: multiple camera tracking](http://synesis.ru/en/surveillance/contents/mctintro)
        - [AVE Video Fusion](http://www.sentinelave.com/ave.html)
        - [Video Surveillance and Monitoring (VSAM)](http://www.cs.cmu.edu/~vsam/OldVsamWeb/vsamhome.html)

## Saliency detection
- [15 ICCV - Minimum Barrier Salient Object Detection at 80 FPS](http://cs-people.bu.edu/jmzhang/fastmbd.html): with exe

## Image feature detection and description [^1]
### Keypoint detectors
- [FAST](http://www.edwardrosten.com/work/fast.html): high-speed corner detector, in OpenCV
- [AGAST](http://www6.in.tum.de/Main/ResearchAgast): even faster than the FAST. A multi-scale version of this method is used for the BRISK descriptor (10 ECCV).

### Binary descriptors
- [BRIEF](http://cvlab.epfl.ch/research/detect/brief) – fast and accurate interest point descriptor (not invariant to rotations and scale)
- [ORB](http://docs.opencv.org/modules/features2d/doc/feature_detection_and_description.html) – Oriented-Brief descriptor (invariant to rotations, but not scale), in OpenCV
- [BRISK](http://docs.opencv.org/modules/features2d/doc/feature_detection_and_description.html) – efficient binary descriptor invariant to rotations and scale, in OpenCV
- [FREAK](http://docs.opencv.org/modules/features2d/doc/feature_detection_and_description.html) – faster than BRISK (invariant to rotations and scale), in OpenCV

### SIFT & SURF Implementations
- SIFT: [Original code by David Lowe](http://www.cs.ubc.ca/~lowe/keypoints/) |  [OpenCV](http://docs.opencv.org/modules/nonfree/doc/feature_detection.html) | [OpenSIFT](http://robwhess.github.com/opensift/) | [GPU](http://cs.unc.edu/~ccwu/siftgpu/)
- SURF: [Herbert Bay's code](http://www.vision.ee.ethz.ch/~surf/index.html) | [OpenCV](http://docs.opencv.org/modules/nonfree/doc/feature_detection.html) | [CUDA]((https://www.mpi-inf.mpg.de/departments/computer-vision-and-multimodal-computing/research/object-recognition-and-scene-understanding/cuda-surf-a-real-time-implementation-for-surf/)

### Other Local Feature Detectors and Descriptors
- [VGG Affine Covariant features](http://www.robots.ox.ac.uk/~vgg/research/affine/) – Oxford code for various affine covariant feature detectors and descriptors
- [LIOP descriptor](http://zhwang.me/publication/liop/index.html) – Local Intensity Order Pattern (LIOP) descriptor
- [Local Symmetry Features](http://www.cs.cornell.edu/projects/symfeat/) – matching of local symmetry features under large variations in lighting, age, and genTocHereg style

### Global Image Descriptors
- GIST: [MATLAB](http://people.csail.mit.edu/torralba/code/spatialenvelope/) | [C++](http://lear.inrialpes.fr/src/lear_gist-1.2.tgz) (Lear's version)
- [CENTRIST](https://sites.google.com/site/wujx2001/home/libhik) (in `libHIK`) – global visual descriptor for scene categorization and object detection. Another version can be found [here](http://dovgalecs.com/blog/centrist-visual-descriptor-for-indoors-localization/).

## Image deblur
- Overview by cwang: [Deblur-related Materials](http://i.cs.hku.hk/~cwang/deblur/index.html)

## Image Dehaze
- [09 CVPR - Single Image Haze Removal using Dark Channel Prior](http://research.microsoft.com/en-us/um/people/kahe/cvpr09/index.html) | [Blog](http://blog.csdn.net/polly_yang/article/details/48933383)

## Steoro matching
- [14 PAMI - Stereo Matching Using Tree Filtering](http://www.cs.cityu.edu.hk/~qiyang/publications/cvpr-12/pami/)
- [04 CVPR - Eﬃcient Belief Propagation for Early Vision](http://cs.brown.edu/~pff/bp/index.html)

## Filtering
- Bilateral Filtering: [14 PAMI - Hardware-Efficient Bilateral Filtering for Stereo Matching](http://www.cs.cityu.edu.hk/~qiyang/publications/hebf/) (integrated)

---
[^1]: Source page of SYSU: http://gitl.sysu.edu.cn/downloads.
