+++
title = "Matrices"
description = ""
date = 2025-01-11
+++
---
(index here)

---
# 2D Matrices
## Basics
To define a position in 2D space, we use 2 scalar values: \
$$
\normalsize \text{(diagram)}
$$
$$
\definecolor{Red}{RGB}{250,32,32}
\definecolor{Green}{RGB}{22,190,46}
\definecolor{Blue}{RGB}{51,46,250}
\Large p=(\textcolor{Red}{x},\\, \textcolor{Green}{y})
$$
However 2 scalar values alone don't describe a position. \
We need to define which direction each of these scalars are pointing in.

Our position is actually defined as:
$$
\large
\begin{aligned}
p = x \textcolor{Red}{\begin{pmatrix} 1 \\\\ 0 \end{pmatrix}} + y \textcolor{Green}{\begin{pmatrix} 0 \\\\ 1 \end{pmatrix}}
\end{aligned}
$$
We now have the **"basis vectors"** of our 2D space, shown in red and green. \
The basis vectors represent the axes we work with, defining our space (the 2D plane).

Arranging these 2 vectors into a matrix gives us:
$$
\large
\begin{bmatrix}
  \textcolor{Red}{1} & \textcolor{Green}{0} \\\\
  \textcolor{Red}{0} & \textcolor{Green}{1}
\end{bmatrix}
$$
A matrix is simply a definition of a space. Reading the columns of a matrix gives us the basis vectors (i.e the axes).
So the complete definition of our original point $p$ is:
$$
\large
\begin{align}
p = 
\begin{bmatrix}
  \textcolor{Red}{1} & \textcolor{Green}{0} \\\\
  \textcolor{Red}{0} & \textcolor{Green}{1}
\end{bmatrix}
\circ \begin{pmatrix} x \\\\ y \end{pmatrix}
\end{align}
$$

&nbsp;

## Matrices as Transformations
### Identity Matrix
The matrix formed in the last section is called the identity matrix:
$$
\large
\begin{align}
I_2 = \begin{bmatrix}
1 & 0 \\\\
0 & 1 \\\\
\end{bmatrix}
\end{align}
$$
The identity matrix represents a space with no transformation. \
Whenever we write a vector as a position, it is assumed that we are working in this space.
Here, the "2" denotes 2 dimensions.

### Scaling
We can change the basis vectors to transform our space about the origin. \
First, scaling the space is as simple as multiplying a basis vector by a scalar. \
For example, scaling by $2$ in the $x$ direction gives us:
$$
\large
\begin{aligned}
\textcolor{Red}{\begin{pmatrix} 2 \\\\ 0 \end{pmatrix}}, \textcolor{Green}{\begin{pmatrix} 0 \\\\ 1 \end{pmatrix}}
\rightarrow
\begin{bmatrix}
\textcolor{Red}{2} & \textcolor{Green}{0} \\\\
\textcolor{Red}{0} & \textcolor{Green}{1}
\end{bmatrix}
\end{aligned}
$$
$$
\normalsize \text{(diagram)}
$$
### Rotation
We can also change the direction of the basis vectors. \
To represent a rotation **without scaling**, we must find basis vectors of length $1$ for our rotated space.

For example, a $45^\text{o}$ anti-clockwise rotation has the following basis vectors:
$$
\normalsize \text{(diagram)}
$$
$$
\large
\begin{aligned}
\textcolor{Red}{
\begin{pmatrix} \frac{2}{\sqrt{2}} \\\\ \frac{2}{\sqrt{2}} \end{pmatrix}},
\textcolor{Green}{
\begin{pmatrix} \frac{-2}{\sqrt{2}} \\\\ \frac{2}{\sqrt{2}} \end{pmatrix}}
\rightarrow
\frac{1}{\sqrt{2}}
\begin{bmatrix}
\textcolor{Red}{2} & \textcolor{Green}{-2} \\\\
\textcolor{Red}{2} & \textcolor{Green}{2}
\end{bmatrix}
\end{aligned}
$$

## Matrix Multiplication
We've defined some basic transformations using matrices, but to carry out these transformations we must use matrix multiplication. \
The goal is to get a vector multiplied by the **identity matrix**, which will give us its position in normal 2D space.

### Rotating a Point
To rotate the point $\large (x, \\, y)$ by $45^\text{o}$, giving us $\large (x', \\, y')$, we can do the following:
$$
\large
\begin{align}
I_2
\begin{pmatrix}
x' \\\\
y'
\end{pmatrix} =
\frac{1}{\sqrt{2}}
\begin{bmatrix}
\textcolor{Red}{2} & \textcolor{Green}{-2} \\\\
\textcolor{Red}{2} & \textcolor{Green}{2}
\end{bmatrix}
\circ \begin{pmatrix} x \\\\ y \end{pmatrix}
\end{align}
$$
To evaluate this, we can use the basis vectors of our rotation matrix: \
*(remember from the first section, $x$ and $y$ are just multiples of the basis vectors)*
$$
\begin{align}
\begin{pmatrix}
x' \\\\
y'
\end{pmatrix} = 
\frac{1}{\sqrt{2}}
\(
x
\textcolor{Red}{
\begin{pmatrix} 2 \\\\ 2 \end{pmatrix}} + y
\textcolor{Green}{
\begin{pmatrix} -2 \\\\ 2 \end{pmatrix}}) =
\frac{2}{\sqrt{2}}
\begin{pmatrix}
\textcolor{Red}{2}x \textcolor{Green}{-2}y \\\\
\textcolor{Red}{2}x + \textcolor{Green}{2}y
\end{pmatrix}
\end{align}
$$

### Rotating Multiple Points
If we have multiple column vectors, we can multiply them using the same method.
$$
\begin{align}
\frac{1}{\sqrt{2}}
\begin{bmatrix}
\textcolor{Red}{2} & \textcolor{Green}{-2} \\\\
\textcolor{Red}{2} & \textcolor{Green}{2}
\end{bmatrix}
\circ \begin{pmatrix} 0 & 0 & 1 & 1 \\\\ 0 & 1 & 1 & 0 \end{pmatrix} =
\end{align}
$$
$$
\frac{1}{\sqrt{2}}
\begin{pmatrix}
(\textcolor{Red}{2} \times 0 \textcolor{Green}{ - 2} \times 0) &
(\textcolor{Red}{2} \times 0 \textcolor{Green}{ - 2} \times 1) &
(\textcolor{Red}{2} \times 1 \textcolor{Green}{ - 2} \times 1) &
(\textcolor{Red}{2} \times 1 \textcolor{Green}{ - 2} \times 0)
\\\\
(\textcolor{Red}{2} \times 0 \textcolor{Green}{ + 2} \times 0) &
(\textcolor{Red}{2} \times 0 \textcolor{Green}{ + 2} \times 1) &
(\textcolor{Red}{2} \times 1 \textcolor{Green}{ + 2} \times 1) &
(\textcolor{Red}{2} \times 1 \textcolor{Green}{ + 2} \times 0)
\end{pmatrix} =
$$
$$
\frac{1}{\sqrt{2}}
\begin{pmatrix} 0 & -2 & 0 & 2 \\\\ 0 & 2 & 4 & 2 \end{pmatrix}
$$
$$
\normalsize \text{(diagram)}
$$
In the second line of working out, you can see how the matrix is repeated 4 times, once for each point - matrix by matrix multiplication is just the same as matrix by vector multiplication.

## Further 2D Transformations
### Rotation
We can form a generalised rotation matrix with the same method as before:
$$
\normalsize \text{(diagram)}
$$
$$
R=\begin{bmatrix}
\cos{\theta} & -\sin{\theta} \\\\
\sin{\theta} & \cos{\theta}
\end{bmatrix}
$$
### Translation
Unfortunately there is no way of changing the basis vectors such that the origin will move, however it is often useful to represent translation using a matrix in graphics programming. To represent translation, we need to borrow from the 3rd dimension. \
Suppose our 2D space lies at $\large z=1$.
$$
\normalsize \text{(diagram)}
$$
By modifying the 3rd vector $\textcolor{Blue}{\begin{pmatrix}0 \\\\ 0\\\\ 1\end{pmatrix}}$ to add a translation of ${\begin{pmatrix}a \\\\ b \end{pmatrix}}$, we get the following space:
$$
\normalsize \text{(diagram)}
$$
$$
\begin{bmatrix}
\textcolor{Red}{1} & \textcolor{Green}{0} & \textcolor{Blue}{a} \\\\
\textcolor{Red}{0} & \textcolor{Green}{1} & \textcolor{Blue}{b} \\\\
\textcolor{Red}{0} & \textcolor{Green}{0} & \textcolor{Blue}{1} \\\\
\end{bmatrix}
$$
Then when carrying out transformations using this matrix, we need to ensure we set $z=1$, for example:
$$
\begin{bmatrix}
\textcolor{Red}{1} & \textcolor{Green}{0} & \textcolor{Blue}{a} \\\\
\textcolor{Red}{0} & \textcolor{Green}{1} & \textcolor{Blue}{b} \\\\
\textcolor{Red}{0} & \textcolor{Green}{0} & \textcolor{Blue}{1} \\\\
\end{bmatrix}
\circ
\begin{pmatrix}
x \\\\
y \\\\
1 \\\\
\end{pmatrix} =
\begin{pmatrix}
x + \textcolor{Blue}{a} \\\\
y + \textcolor{Blue}{b} \\\\
1 \\\\
\end{pmatrix}
$$

---

# 3D Matrices
