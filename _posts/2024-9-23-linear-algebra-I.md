---
layout: post
title: "#1 Linear Algebra - part I 🚀"
categories: Math Finance
---

# 📊 Understanding Matrices, Eigenvalues, and Singular Value Decomposition (SVD)

Matrices, Eigenvalues, and Singular Value Decomposition (SVD) are fundamental concepts in linear algebra, with applications ranging from data science to machine learning, physics, finance, and beyond. In this post, we'll break down these topics in a beginner-friendly way, explaining what they are, how they work, and why they're important. By the end, you'll have a solid foundation to build on for more advanced topics. 🎓

## 1. 🟦 Matrices and Their Operations

### What is a Matrix? 🤔
A **matrix** is a way to organize numbers in a rectangular grid format. The numbers are arranged in rows and columns, and each number in the matrix is called an **element**. Think of a matrix like a table where the rows represent one dimension, and the columns represent another.

For example, here's a **2x3 matrix** (2 rows and 3 columns):

\[
\begin{pmatrix}
1 & 2 & 3 \\
4 & 5 & 6
\end{pmatrix}
\]

Each element can be referred to by its position, like in a spreadsheet. In this case, the number in the first row and second column is 2.

### Why Are Matrices Important? 🌍
Matrices are incredibly useful for solving systems of equations, representing datasets, performing transformations (rotations, scaling, etc.), and much more. In computer science, they are widely used in machine learning models and graphics rendering, while in finance, matrices help model portfolios, stocks, or any financial system with multiple variables.

### Basic Matrix Operations 🧮
There are a few operations you can perform on matrices to manipulate them.

#### ➕ Matrix Addition and Subtraction
You can **add** or **subtract** two matrices, but only if they have the same size (i.e., the same number of rows and columns). You simply add or subtract corresponding elements.

For example:

\[
\begin{pmatrix}
1 & 2 \\
3 & 4
\end{pmatrix}
+
\begin{pmatrix}
5 & 6 \\
7 & 8
\end{pmatrix}
=
\begin{pmatrix}
6 & 8 \\
10 & 12
\end{pmatrix}
\]

#### ✖️ Scalar Multiplication
In **scalar multiplication**, you multiply every element of the matrix by a single number (the scalar).

For example, multiplying a matrix by 2:

\[
2 \times
\begin{pmatrix}
1 & 2 \\
3 & 4
\end{pmatrix}
=
\begin{pmatrix}
2 & 4 \\
6 & 8
\end{pmatrix}
\]

#### 🤯 Matrix Multiplication
This operation is a bit more complicated but extremely useful. You can multiply two matrices together if the number of columns in the first matrix matches the number of rows in the second matrix. The resulting matrix is formed by multiplying the rows of the first matrix by the columns of the second matrix.

For example:

\[
\begin{pmatrix}
1 & 2 \\
3 & 4
\end{pmatrix}
\times
\begin{pmatrix}
5 & 6 \\
7 & 8
\end{pmatrix}
=
\begin{pmatrix}
(1 \times 5 + 2 \times 7) & (1 \times 6 + 2 \times 8) \\
(3 \times 5 + 4 \times 7) & (3 \times 6 + 4 \times 8)
\end{pmatrix}
=
\begin{pmatrix}
19 & 22 \\
43 & 50
\end{pmatrix}
\]

Matrix multiplication is fundamental in many fields. For example:

- **Finance** 💰: Portfolio management uses matrices to calculate the performance of multiple investments over time, adjusting weights or values as the portfolio evolves.
- **Computer Science** 💻: Matrix multiplication is the backbone of neural networks, where layers of neurons are connected via weights (which are stored in matrices), and each forward pass through the network involves matrix operations.

#### 🔄 Transpose of a Matrix
The **transpose** of a matrix is found by flipping the matrix over its diagonal. This means rows become columns and vice versa.

For example:

\[
\text{Transpose of }
\begin{pmatrix}
1 & 2 & 3 \\
4 & 5 & 6
\end{pmatrix}
=
\begin{pmatrix}
1 & 4 \\
2 & 5 \\
3 & 6
\end{pmatrix}
\]

The transpose is useful in many mathematical operations, particularly when working with dot products, projections, and orthogonal transformations.

---

## 2. 🔍 Eigenvalues and Eigenvectors

### What Are Eigenvalues and Eigenvectors? 🎯

In simple terms, **eigenvalues** and **eigenvectors** help us understand how matrices act on vectors (arrows that have both a direction and magnitude). 

Imagine you have a matrix that represents some kind of transformation. For most vectors, when you apply the matrix, the vector changes both in direction and length. But **eigenvectors** are special vectors that only get **stretched or shrunk** when you apply the matrix, without changing direction. The amount they get stretched or shrunk by is the **eigenvalue**.

So, for a given matrix \( A \), if \( v \) is an eigenvector and

