# Performance Lab
Write Up/Code for the CMU Performance Assignment

## Introduction
This assignment deals with optimizing memory intensive code. Image processing offers many examples of functions that can benefit from optimization. In this lab, we will consider two image processing operations: rotate, which rotates an image counter-clockwise by 90 ◦ , and smooth, which “smooths” or “blurs” an image.

For this lab, we will consider an image to be represented as a two-dimensional matrix M , where M i,j denotes the value of (i, j)th pixel of M. Pixel values are triples of red, green, and blue (RGB) values. We will only consider square images. Let N denote the number of rows (or columns) of an image. Rows and columns are numbered, in C-style, from 0 to N − 1.

Given this representation, the rotate operation can be implemented quite simply as the combination of the following two matrix operations:

* Transpose: For each (i, j) pair, M i,j and M j,i are interchanged.
* Exchange rows: Row i is exchanged with row N − 1 − i.

This combination is illustrated in Figure 1. The smooth operation is implemented by replacing every pixel value with the average of all the pixels around it (in a maximum of 3 × 3 window centered at that pixel). Consider Figure 2. The values of pixels M2[1][1] and M2[N-1][N-1] are given below:

![Formula for Smoothing](https://github.com/csuchico-csci551/Performance-Lab/raw/master/images/formula1.png "Formula for Smoothing")

![Figure 1](https://github.com/csuchico-csci551/Performance-Lab/raw/master/images/figure1.png "Figure 1")
Figure 1: Rotation of an image by 90◦ counterclockwise

![Figure 2](https://github.com/csuchico-csci551/Performance-Lab/raw/master/images/figure2.png "Figure 2")
Figure 2: Smoothing an image

## Logistics

You must work on your own in solving the problems for this assignment. The only “hand-in” will be electronic. Any clarifications and revisions to the assignment will be posted on Piazza.

## Handout Instructions

Start by copying perflab-handout.tar to a protected directory in which you plan to do your work. Then give the command:
```bash
$ tar xvf perflab-handout.tar.
```

This will cause a number of files to be unpacked into the directory. The only file you will be modifying and handing in is *kernels.c*. The driver.c program is a driver program that allows you to evaluate the performance of your solutions. Use the command make driver to generate the driver code and run it with the command ./driver.

Looking at the file kernels.c you’ll notice a C structure team into which you should insert the requested identifying information about the one individual comprising your programming team. **Do this right away so you don’t forget, also the code won't compile until you do**.

## Implementation Overview

### Data Structures

The core data structure deals with image representation. A pixel is a struct as shown below:

```C
typedef struct {
  unsigned short red; /* R value */
  unsigned short green; /* G value */
  unsigned short blue; /* B value */
} pixel;
```
As can be seen, RGB values have 16-bit representations (“16-bit color”). An image I is represented as a one- dimensional array of pixels, where the (i, j)th pixel is I[RIDX(i,j,n)]. Here n is the dimension of the image matrix, and RIDX is a macro defined as follows:

```C
#define RIDX(i,j,n) ((i)*(n)+(j))
```

See the file *defs.h* for this code.

### Rotate

The following C function computes the result of rotating the source image src by 90◦ and stores the result in destination image dst. dim is the dimension of the image.

```C
void naive_rotate(int dim, pixel *src, pixel *dst) {
  int i, j;
  for(i=0; i < dim; i++)
    for(j=0; j < dim; j++)
      dst[RIDX(dim-1-j,i,dim)] = src[RIDX(i,j,dim)];
  return;
}
```

The above code scans the rows of the source image matrix, copying to the columns of the destination image matrix. Your task is to rewrite this code to make it run as fast as possible using techniques like code motion, loop unrolling and blocking.
See the file *kernels.c* for this code.

### Smooth

The smoothing function takes as input a source image src and returns the smoothed result in the destination image dst. Here is part of an implementation:

```C
void naive_smooth(int dim, pixel *src, pixel *dst) { int i, j;
  for(i=0; i < dim; i++)
    for(j=0; j < dim; j++)
      dst[RIDX(i,j,dim)] = avg(dim, i, j, src); /* Smooth the (i,j)th pixel */
  return;
}
```

The function avg returns the average of all the pixels around the (i,j)th pixel. Your task is to optimize smooth (and avg) to run as fast as possible. (Note: The function avg is a local function and you can get rid of it altogether to implement smooth in some other way.)

This code (and an implementation of avg) is in the file *kernels.c.*

### Performance measures

Our main performance measure is CPE or Cycles per Element. If a function takes C cycles to run for an image of size N × N, the CPE value is C/N2. Table 1 summarizes the performance of the naive implementations shown above and compares it against an optimized implementation. Performance is shown for for 5 different values of N. All measurements were made on the Pentium III Xeon Fish machines.

The ratios (speedups) of the optimized implementation over the naive one will constitute a score of your implementation. To summarize the overall effect over different values of N, we will compute the geometric mean of the results for these 5 values. That is, if the measured speedups for N = {32, 64, 128, 256, 512} are R32 , R64 , R128 , R256 , and R512 then we compute the overall performance as seen in the following formula:

![Performance Formula](https://github.com/csuchico-csci551/Performance-Lab/raw/master/images/formula2.png "Formula for Performance Scoring")

| Test case        | 1 | 2 | 3 | 4 | 5 |  |
| ------------- |----|----|----|----|----|----|
| Method N     | 64 |128| 256| 512 |1024| Geom. Mean|
|Naive rotate (CPE)|14.7| 40.1| 46.4| 65.9| 94.5| |
|Optimized rotate (CPE)|8.0| 8.6| 14.8| 22.1| 25.3| |
|Speedup (naive/opt)|1.8| 4.7| 3.1| 3.0| 3.7|3.1|
||||||||
|Method N|32 |64| 128| 256| 512|Geom. Mean|
|Naive smooth (CPE)|695| 698| 702| 717| 722| |
|Optimized smooth (CPE)|41.5 |41.6 |41.2 |53.5 |56.4| |
|Speedup (naive/opt)|16.8 |16.8| 17.0| 13.4| 12.8|15.2|

Table 1: CPEs and Ratios for Optimized vs. Naive Implementations
