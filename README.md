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
  unsigned short red; /* R value */ unsigned short green; /* G value */
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
