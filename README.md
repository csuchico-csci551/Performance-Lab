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

![Figure 2](https://github.com/csuchico-csci551/Performance-Lab/raw/master/images/figure2.png "Figure 2")
