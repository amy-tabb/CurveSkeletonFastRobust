# CurveSkel-Tabb-Medeiros

### Newer release in another repository
This repository contains a stable, older release.  Want the new one?  Go to [CurveSkel-Tabb-Medeiros-2018](https://github.com/amy-tabb/CurveSkel-Tabb-Medeiros-2018).

# Underlying ideas -- the paper

This README file is to accompany code for curve skeletonization of elongated objects that may have noisy surfaces, produced by Amy Tabb and Henry Medeiros as a companion to their paper:
	*Fast and robust curve skeletonization for real-world elongated objects*
````latex
@INPROCEEDINGS{Tabb18Fast,
author={Amy Tabb and Henry Medeiros},
booktitle={2018 IEEE Winter Conference on Applications of Computer Vision (WACV)},
title={Fast and robust curve skeletonization for real-world elongated objects},
year={2018},
pages={1935-1943},
doi={10.1109/WACV.2018.00214},
month={March},}
````

This paper is also available from arXiv:1702.07619 [cs.CV] [here](https://arxiv.org/pdf/1702.07619.pdf) -- including the supplementary material.  The arxiv version is identical in content to the IEEE version.

# Citing the code

The code may be used without restriction. If the results of the code are used as a part of a system described in a publication, we request that the authors cite the published paper at a minimum.  If you use the implementation contained in this github release as a part of a publication, we'd be thrilled if you cited the code release as well.  The citation for the code itself is: 

````latex
@electronic{tabb2018skel_code,
author = {Tabb, Amy},
year = {2018},
title = {Code from: Fast and robust curve skeletonization for real-world elongated objects},
doi = {10.15482/USDA.ADC/1399689},
owner = {Ag Data Commons},
howpublished= {\url{http://dx.doi.org/10.15482/USDA.ADC/1399689}}
} 
````

(Other citation styles are fine -- this is one that worked for us, but we're not totally happy with it.)

**Finally, all organizations have ways of assessing impact, and as someone whose work will never see the light of patenting since I develop algorithms, if this curve skeleton algo is useful to your work in some way and you are able to let me know in a one line email, that would be fabulous.**

# Minimal working examples
Minimal working examples are available as a part of this Github repository, in the `demo_files` directory.  Also, this release is part of a data release hosted at Ag Data Commons, and more information may be available [there](http://dx.doi.org/10.15482/USDA.ADC/1399689).

The model may be only used for evaluation and debugging purposes of the code, and not used in any other published, or 3D printed, work without getting my permission. However, no gaurantees are expressed or implied of the code or the model.

Last update 27 July 2018.
Comments/Bugs/Problems: amy.tabb@ars.usda.gov

# Docker version

C++ compiling, linking, running not your cup of tea, or are you on another OS?  I just completed a Docker release -- [here](https://hub.docker.com/r/amytabb/curveskel-tabb-medeiros-docker/).  However, this README.md is going to be your friend, so come back.

# Compiling, Linking, Running
Basic instructions for compilation and linking:

1. This code has been written and tested on Ubuntu 14.04 and Ubuntu 16.04, using Eclipse CDT as the IDE, and is written in C/C++.  


2. This code is dependent on the >= OpenCV-3.* libraries and OpenMP library (libgomp).  These libraries should be in the include path, or specified in your IDE.


3. Compiler flags: we use OpenMP for parallelization and the C++11 standard.  Note that Eclipse's indexer marks some items from C++11 as errors (but still compiles).  

The flags needed using the gnu compiler, openmp, and the C++11 standard are: `-fopenmp  -std=gnu++11`

though depending on the compiler used, you may need different [flags](https://www.dartmouth.edu/~rc/classes/intro_openmp/compile_run.html)
	
4. 	libraries needed for linking are:
	gomp   [OpenMP]
	opencv_core [OpenCV]
	opencv_legacy [deprecated, some versions of OpenCV and not needed] 
	opencv_highgui
	opencv_imgproc
	opencv_imgcodecs

5. **Note before going further -- if in Ubuntu, you need to `apt-get install build-essential` to get C++ compiler(s) that are OpenMP enabled.  If you are on a Mac, you will need something comparable to build-essential.**
	
6. Easy way to build in Ubuntu with [Eclipse CDT](https://www.eclipse.org/cdt/) with git support (Egit): 
- `git clone` into the directory of choice.  
- Create new Managed C++ project, for the directory, select the directory where the `CurveSkel-Tabb-Medeiros` was cloned into.
- The compiler and linker flags for OpenCV 4.0 will be imported.  Build and you're ready to run!

7. Easy way to build without Eclipse CDT:

```
g++ src/*.cpp -o curve_skel -fopenmp -std=gnu++11 -Wall -I/usr/local/include -lgomp -lopencv_core -lopencv_imgproc -lopencv_imgcodecs
```

The executable `curve_skel` is created.  If you used method 6., the location will be in `Debug`.

## Running

### File formats 
 
Instructions for use:
We use a file format for representing models that is composed of a text file containing the model points `0.txt` and bounding box in file `BB.txt`.  
We include samples of these two types of files in the "examples" folder.  We also have some conversion functions (see 3 in this section).

### Run with default threshold, our file format
1. To run the code using the default threshold (1e-12) for rejecting spurious segments, you call with one argument, the name of the directory containing `0.txt` and `BB.txt`:
```
./program_name directory
```

### Run with selected threshold, our file format
2. To run the code with different values of the threshold, you call with two argmuents, the directory name followed by the threshold value [0, 1]:
```
./program_name directory threshold
```

### Run from an image stack representation, write to an image stack representation.
3. We have chosen the image sequence representation for conversion.  To convert models from any other format, one can use ImageJ/Fiji to load and then save as an image
sequence.  Once a sequence is created, save in a folder titled "rawimages" within the directory.  Then call with three arguments:
```
./program_name directory threshold 1
```

We have included in the `demo_files` folder an image sequence (`ConversionFromImageSequence`).  The result is saved in a folder called `processedimages`, which one can then load with ImageJ/Fiji to visualize in
one's preferred environment.  The result is also saved in the format described below.

## Results

Results format:
The results are written as .ply meshes.  One can view them with free viewer [Meshlab].  A sample of results is shown in the `demo_files/A_Result` directory.

- The `paths_TabbMedeiros.ply` file shows the paths computed by the algorithm.
- The `skel_TabbMedeiros.ply` shows the 1D skeleton voxels.
- The `initial.ply` file shows a mesh of the original object.  This may be important for verifying that your model files are set up correctly.  Also, note that this code only computes the curve skeleton of the largest connected component of the model file. This can easily be altered by iterating over all of the components, or feeding different components to the algorithm. `initial.ply` records the connected component used as input to the algorithm.
- `details.txt` shows some record of the algorithm's progress and the size of the input.  For instance, 
```
time 3.294 seconds 
Number of occupied voxels 88798
Number possible voxels 45240000
Number of proposed tips 145
Threshold for spurious segment classification 1e-12
```
time is the run time for the algorithm to compute the curve skeleton, not including load and write time.  The initial model contained 88,798 voxels.  The user enter threshold 1e-12 or used the default, etc.




