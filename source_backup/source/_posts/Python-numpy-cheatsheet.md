---
title: Python-numpy-cheatsheet
date: 2025-03-06 13:01:00
index_img: /img/cover/numpy.jpg
excerpt: This Blog focuses on the most frequently used python modules numpy, regarding its basic usage and several applications.
categories:
  - Code
  - Python
tags:
  - Tutorial
  - Finished
  - Python
  - Numpy
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Numpy CheatSheet

See the repo on my github: https://github.com/xiyuanyang-code/numpy_tutorial.

## Introduction

> This section is copied from `README.md` part in my github repo.

### Learning Numpy within 30 minutes!

😊Quickly get started with basic NumPy operations in 30 minutes!

See https://xiyuanyang-code.github.io/posts/Python-numpy-cheatsheet/ for more details.

#### Introduction

NumPy is the fundamental package for scientific computing in Python. It is a Python library that provides a multidimensional array object, various derived objects (such as masked arrays and matrices), and an assortment of routines for fast operations on arrays, including mathematical, logical, shape manipulation, sorting, selecting, I/O, discrete Fourier transforms, basic linear algebra, basic statistical operations, random simulation and much more. [^1]

This repo provides with the basic numpy tutorial with `.ipynb` file, aiming to teach greenhands to quickly get started with basic NumPy operations using Jupyter Notebook interaction in 30 minutes.

#### Installation


This command is for importing the numpy module in your python scripts:
```bash
pip install numpy==1.26.4
```

You can also use the `requirements.txt` file in this repo.
```bash
pip install -r requirements.txt
```

After the installation, run the first code cell in `numpy_tutorial.ipynb` to check if the output is correct. (`1.26.4`)

#### Usage

The tutorial includes several parts:

- Creating arrays and matrix
- Checking parameters of Arrays
- Accessing and modifying elements
	- Slices
	- Modifications
- Repetition, Copying and Reshaping
	- Repeat
	- Copy
	- Reshape
- Linear Algebra and other mathematical operations
	- Basic Mathematical operations
	- Linear Algebra
- Statics
	- Boolean Mask
- Advanced Usage
	- Loading data from files

Go to the juypter notebook for more details!

#### In the future...

We will finish the `/demo` directory in the future, which will provides you with practical applications of numpy.

#### References

[^1]: https://numpy.org/doc/stable/

## Basic Usage

> This section is copied from `numpt_tutorial.ipynb` section in my github repo.

# Numpy tutorial

## Preliminaries

This command is for importing the numpy module in your python scripts:

```bash
pip install numpy
```

You can use functions in numpy via `np.XX` (e.g. `np.array`)

You can replace it with:

- import numpy as (anything word you like)
- from numpy import numpy (danger! This may cause conflicts in different functions. I don't recommend for this way of importing numpy)

In [1]:

```python
# make sure you have installed numpy via:
# pip install numpy

import numpy as np
```

## Creating arrays and matrix

You can create **arrays** in `numpy` of different dimensions, which is one of the most frequently used commands in numpy.

- Creating arrays manually

	You can create arrays manually by **writing all elements into the arrays**.

In [ ]:

```python
# Creating arrays manually

# One dimension (like a vector storing int value)
array_1d = np.array([1,2,3,4])

# Two dimensions (4*4)
array_2d = np.array([[1,2,3,4],[5,6,7,8],[9,10,11,12],[13,1,4,16]])

# Three dimensions (more complex)
array_3d = np.array([[[1,2],[3,4]],[[5,6],[7,8]]])

# Visualize
print(f"Showing arrays of one-dimension: \n",array_1d)
print(f"Showing arrays of two-dimensions: \n",array_2d)
print(f"Showing arrays of three-dimensions: \n",array_3d)
```

- Creating several **templates**

	It's time-consuming to write all elements in your python scripts if the size or the dimensions of arrays becomes very large. (More bigger the dimension is, there are more square backets!)

	Thus, you can use the templates in `numpy` to initilize an array automatically.

In [ ]:

```python
# Creating several templates

# If all elements of the matrix is of the same initial value:

# For zero-matrix
zero_int_matrix = np.zeros((5,5), dtype = 'int64')
zero_double_matrix = np.zeros((4,5), dtype = 'float64')
zero_int_bigger_matrix = np.zeros((2,2,2))

print(zero_int_matrix)
print(zero_double_matrix)
print(zero_int_bigger_matrix)


# For matrix filled with ones
ones_matrix = np.ones((4,4,2), dtype='int32')
print(ones_matrix)

# Identity Matrix
indentity_matrix = np.identity(6)
print(indentity_matrix)
```

In [ ]:

```python
# Filled with a specific number
tobefilled = 64

# Using full to fill an array with a given size (like 3*4)
filled_array = np.full((3,4), fill_value= tobefilled, dtype = 'int64')
print(filled_array)

# Using full_like to return a full array with the same shape and type as a given array.
filled_like_1 = np.full_like(filled_array, fill_value=tobefilled, dtype='float64')
filled_like_2 = np.full_like(filled_array, fill_value= 1)

print(filled_like_1)
print(filled_like_2)
```

Moreover, you can generate arrays **randomly** using the `random` module in numpy.

- `np.random.rand` function is used to generate an array of a given size with a a uniform distribution over [0, 1).
- `randint` function is used to return random integers from a boundary of [a,b).

In [ ]:

```python
# Generating random arrays

# Create an array of the given shape and populate it with random samples from a uniform distribution over [0, 1).
random_decimals = np.round(np.random.rand(10, 10), 2)
print(random_decimals)

# Return random integers from low (inclusive) to high (exclusive).
random_integers = np.random.randint(-4, 8, size=(3, 3))
print(random_integers)
```

## Checking parameters of Arrays

You can checking several basic information of an array using `print()` function.

- print the array: It will show all elements of the array.
- `dtype`: The data type of the array.
- `ndim`: The dimensions of the array.
- `shape`: The size of the array.
- `itemsize`: The size of the single element in the array.
- `nbytes`: The total size of the array.

In [ ]:

```python
# Checking parameters of Arrays

checked_array = np.round(np.random.rand(4,4,4))

print(checked_array)
print(checked_array.dtype)
print(checked_array.ndim)
print(checked_array.shape)
print(checked_array.itemsize)
print(checked_array.nbytes)
```

Wow! You have sucessfully created various arrays! In the following chapter, you will learn how to **operate arrays and make some calculations**.

## Accessing and modifying elements

### Slices

We can get the slices of the array and access specific elements by using `[]` operator. The scripts below shows the fundamental usage of it.

In [ ]:

```python
get_element = np.round(np.random.rand(4,4,4),3)

# See the whole array and its size.
# print(get_element)
print(get_element.ndim, get_element.shape)

# Slices
print("\n")
sliced_array_1 = get_element[0]
print(sliced_array_1)
print(sliced_array_1.ndim, sliced_array_1.shape)

print("\n")
sliced_array_1 = get_element[0,0]
print(sliced_array_1)
print(sliced_array_1.ndim, sliced_array_1.shape)

print("\n")
sliced_array_1 = get_element[0,0,0]
print(sliced_array_1)
print(sliced_array_1.ndim, sliced_array_1.shape)
```

From the example shown above, we know that `[]` can get an array's slices and decrease array's dimensions. It's just like **peeling an onion**!

Regarding the **different dimensions** of an array, you can determine different dimensions by counting square brackets.

Moreover, you can use `:` to make the slices more powerful.

In [ ]:

```python
# The demonstration of using : in slices

print(get_element[0])
print(get_element[0,:])
print(get_element[0,2:,3:])
print(get_element[0,:3])

# Advanced usage: slicing with step size
# -1 means the end
print(get_element[0,0:-1:2])
```

The colon `:` operator is used for slicing, which enables you to select a range of elements.

For example, array[start:end] retrieves elements from the index start to end-1. If you use : alone, like array[:], it returns the entire array. You can also specify a step, such as array[start​\:end:step], which selects elements at regular intervals.

Additionally, when working with multi-dimensional arrays, you can use a comma to separate indices for each dimension, like array[row_index, column_index]. The colon can be applied to each dimension, allowing for complex selections, such as array[:, 1:3], which selects all rows but only columns 1 and 2.

For me, I prefer to memorize `:` as a **greedy symbol**, which means get all the index from the current position to the end (or the beginning).

For example, `get_element[0,2:,3:]` means for the second dimension, we just want from the 2 (included) till the end. The third dimension is the same. If `:` is before the number like `get_element[0,:3]`, this means for second dimension, we want (from the begining) to the index 3 (excluded).

### Modifications

You can also modify values (or even slices) of the array by giving the right index.

Make sure **the size of the array doesn't mismatch!**

In [ ]:

```python
# Modification
get_element[0,0,0] = 100

np.set_printoptions(suppress=True)
print(get_element)

get_element[0,0,:] = [20, 20, 20, 20]
print(get_element)
```

In [ ]:

```python
# However, this may cause an error
get_element[0,0,:] = [10, 10]
print(get_element)

'''
Error message:
ValueError: could not broadcast input array from shape (2,) into shape (4,)
'''
```

## Repetition, Copying and Reshaping

You can **repeat**, **reshape** and **copy** arrays!

### Repeat

In [ ]:

```python
base_array = np.round(np.random.rand(5,5),2)
print(base_array)
print(base_array.shape)

print("\n")

# Repeat rows
array_repeated = np.repeat(base_array, 2, axis=0)
print(array_repeated)
print(array_repeated.shape)

print("\n")

# Repeat colomns
array_repeated = np.repeat(base_array, 2,axis=1)
print(array_repeated)
print(array_repeated.shape)
```

### Copy

In [ ]:

```python
# Copying arrays
array_c = np.array([1, 2, 3])
array_d = array_c.copy()  # Create a copy
array_e = array_c          # Reference to original

array_d[0] = 100
array_e[0] = 200
print(array_c)  # Original array remains unchanged
print(array_d)  # Modified copy
print(array_e)  # Reference to original
```

**Attention!** If you use `copy()` function, then the copied array have no correlations with the original array from now on. However, if you simply use the **assignment operator**, then it just creates a **references** of the original array. Modifying one's value will affect the other's!

### Reshape

You can reshape the size of an array by using the `reshape` commands in numpy. For example, you can compress a 25x25 image into a 625-dimensional column vector. But **make sure the reshaped size have same numbers of elements compared with previous ones**.

**Stacking** is a command which is likely to `repeat`, but it can operate on different arrays with same row-length (using hstack) or colomn-length (using vstack).

In [ ]:

```python
# Reshaping arrays
before = np.array([[1, 2, 3, 4], [5, 6, 7, 8]])
after = before.reshape((4, 2))
print(after)

# Vertical stacking
v1 = np.array([1, 2, 3, 4])
v2 = np.array([5, 6, 7, 8])
stacked_vertical = np.vstack([v1, v2, v1, v2])
print(stacked_vertical)

# Horizontal stacking
h1 = np.ones((2, 4))
h2 = np.zeros((2, 2))
stacked_horizontal = np.hstack((h1, h2))
print(stacked_horizontal)
```

## Linear Algebra and other mathematical operations

Here is **Linear Algebra**! You can implement various computations regarding matrix and arrays in numpy.

### Basic Mathematical operations

In [ ]:

```python
array_f = np.array([1, 2, 3, 4])
print(array_f + 2)  # Addition
print(array_f - 2)  # Subtraction
print(array_f * 2)  # Multiplication
print(array_f / 2)  # Division

# Element-wise operations with another array
array_g = np.array([1, 0, 1, 0])
print(array_f + array_g)  # Element-wise addition

# Power and trigonometric functions
print(array_f ** 2)  # Squaring
print(np.cos(array_f))  # Cosine function
```

### Linear Algebra

You can go through the [linalg website](https://numpy.org/doc/2.2/reference/routines.linalg.html) for more caiculations. The python scripts demonstrat several basic operations of it.

In [ ]:

```python
import numpy as np

# Create random matrices
A = np.random.rand(3, 3)
B = np.random.rand(3, 3)

# Matrix Addition
addition_result = np.add(A, B)

# Matrix Subtraction
subtraction_result = np.subtract(A, B)

# Matrix Multiplication
multiplication_result = np.matmul(A, B)
# You can also use A @ B!

# Matrix pow
matrix_exp = np.linalg.matrix_power(A, 3)

# Matrix Transpose
transpose_result = np.transpose(A)

# Matrix Inverse
inverse_result = np.linalg.inv(A)

# Matrix Determinant, rank and trace
determinant_result = np.linalg.det(A)
rank_result = np.linalg.matrix_rank(A)
trace_result = np.trace(A)

# Eigenvalues and Eigenvectors
eigenvalues, eigenvectors = np.linalg.eig(A)

# Solve Linear System (Ax = b)
b = np.random.rand(3)
solution = np.linalg.solve(A, b)

# SVD
U, S, V = np.linalg.svd(A)

# Print results
print("Matrix A:\n", A)
print("Matrix B:\n", B)
print("Addition Result:\n", addition_result)
print("Subtraction Result:\n", subtraction_result)
print("Multiplication Result:\n", multiplication_result)
print("Transpose of A:\n", transpose_result)
print("Inverse of A:\n", inverse_result)
print("Determinant of A:\n", determinant_result)
print("Eigenvalues of A:\n", eigenvalues)
print("Eigenvectors of A:\n", eigenvectors)
print("Solution of Ax = b:\n", solution)
print("SVD\n", U, S, V)
```

## Statics

In [ ]:

```python
stats_array = np.array([[1, 2, 3], [4, 5, 6]])
print(np.min(stats_array))  # Minimum value
print(np.max(stats_array, axis=1))  # Maximum value along rows
print(np.sum(stats_array, axis=0))  # Sum along columns
```

### Boolean Mask

In [ ]:

```python
# Boolean masking example
boolean_mask = ~((stats_array > 2) & (stats_array < 5))
print(boolean_mask)
```

## Advanced Usage

### Loading data from files

You can use `genfromtxt()` function to automatically write the arrays from txt file into numpy. Moreover, you can use `savetxt()` function to save your arrays into other files!

In [ ]:

```python
# Loading data from a CSV file
filedata = np.genfromtxt('data.txt', delimiter=' ')
filedata = filedata.astype('int32')
print(filedata)
```

In [ ]:

```python
# Writing the array to a text file
output_file = 'output_data.txt'
np.savetxt(output_file, filedata, delimiter=' ', fmt='%d')

print(f"Data has been written to {output_file}")
```

## Conclusion

This memo provides a concise overview of NumPy's capabilities, including array creation, manipulation, mathematical operations, and more. For further details, refer to the official [NumPy documentation](https://numpy.org/doc/stable/).

