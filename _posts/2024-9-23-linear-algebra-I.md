---
layout: post
title: "#1 Linear Algebra - part I 🚀"
categories: Math Finance
---

# 📊 Understanding Matrices, Eigenvalues, and Singular Value Decomposition (SVD)

## 1. 🟦 Matrices and Their Operations

### What is a Matrix? 🤔
A **matrix** is a way to organize numbers in a rectangular grid format. Each number in the matrix is called an **element**. For example, here's a **2x3 matrix** (2 rows and 3 columns):

$$[
\begin{pmatrix}
1 & 2 & 3 \\
4 & 5 & 6
\end{pmatrix}
$$]

### Basic Matrix Operations 🧮

#### ➕ Matrix Addition and Subtraction
You can **add** or **subtract** two matrices if they have the same size (i.e., the same number of rows and columns). You add or subtract corresponding elements.

For example:

$$
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
$$

#### ✖️ Scalar Multiplication
In **scalar multiplication**, you multiply every element of the matrix by a single number (the scalar). For example:

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
Matrix multiplication is more complicated but extremely useful. You can multiply two matrices if the number of columns in the first matrix equals the number of rows in the second matrix. The resulting matrix is calculated by multiplying rows from the first matrix by columns of the second matrix.

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

#### 🔄 Transpose of a Matrix
The **transpose** of a matrix flips the matrix over its diagonal. This means rows become columns and vice versa.

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

---

## 2. 🔍 Eigenvalues and Eigenvectors

### What Are Eigenvalues and Eigenvectors? 🎯

Eigenvalues and eigenvectors help us understand how matrices act on vectors. An **eigenvector** is a vector that, when multiplied by a matrix, only gets scaled and doesn't change direction. The scalar value that represents this scaling is the **eigenvalue**.

For a matrix \( A \), if \( v \) is an eigenvector and \( \lambda \) is its corresponding eigenvalue, the following holds:

\[
A \times v = \lambda \times v
\]

This means that multiplying \( A \) by \( v \) gives the same result as multiplying \( v \) by \( \lambda \).

---

## 3. 🧑‍💻 Singular Value Decomposition (SVD)

### What is Singular Value Decomposition? 💾

Singular Value Decomposition (SVD) is a matrix factorization technique. It decomposes any matrix \( A \) into three simpler matrices:

\[
A = U \times \Sigma \times V^T
\]

Where:
- \( U \) and \( V \) are orthonormal matrices,
- \( \Sigma \) is a diagonal matrix containing the singular values of \( A \).

---

### How to Write Mathematical Formulas 📐

To include mathematical formulas with MathJax in Markdown, use the following syntax:

- For an **inline equation**: `$$ V_{sphere} = \frac{4}{3}\pi r^3 $$` will render as \( V_{sphere} = \frac{4}{3}\pi r^3 \).
  
- For a **display-style equation**:

```md
\[
V_{sphere} = \frac{4}{3}\pi r^3
\]
