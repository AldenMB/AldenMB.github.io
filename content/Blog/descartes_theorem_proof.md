Title: An intuitive proof of the Descartes circle theorem
Date: Sept 1 2022
Category: Blog
Tags: Descartes Circle Theorem, Apollo, Metric Geometry
Summary: An original, intuitive proof of the Descartes circle theorem.

I have been a little obsessed with the Descartes circle theorem for a bit over a year now, ever since I came across it when looking for fun fractals to draw. The theorem is essential for quickly computing the circles in an Apollonian gasket. As a reminder, it says that when you have four circles which are pairwise tangent, their radii satisfy
$$
\left(\frac{1}{r_1}+\frac{1}{r_2}+\frac{1}{r_3}+\frac{1}{r_4}\right)
=
2\left(\frac{1}{r_1^2}+\frac{1}{r_2^2}+\frac{1}{r_3^2}+\frac{1}{r_4^2}\right),
$$
where the radius is taken to be negative if the corresponding circle encloses the others. This convention allows us to say $d_{12} = r_1 + r_2$ where $d_{12}$ is the distance between the centers of circles 1 and 2.

I have come up with a proof for this theorem which I like. People have been coming up with proofs of this theorem for centuries, some of them quite elaborate. The most elegant one I have found is due to Jerzy Kocik. It treats the generalized version of the theorem which was [discovered in 2001](https://arxiv.org/abs/math/0101066), which gives a similar relation between the centers of the circles treating them as complex numbers. Kocik's proof is [available on arXiv](https://arxiv.org/abs/1910.09174).

The following proof does not treat the generalized version of the theorem, but it does a better job of giving the intuitive reason for the theorem than any other proof I have seen. It is certainly shorter than any proof I have seen elsewhere.

# Heron's formula and the Cayley-Menger determinant

Most people who have studied any geometry have at least heard of Heron's formula. There are many ways to state it. Here is one of them. It states that for any triangle with area $A$ and side lengths $a$, $b$, and $c$, we have the equation

$$
4A^2 = (a^2+b^2+c^2)^2 - 2(a^4+b^4+c^4).
$$

Perhaps this way of writing it already has stirred some inspiration in you; it certainly bears a passing resemblance to the form in the Descartes theorem. As I say, there are many ways to write Heron's formula. Here is another.

$$
-16A^2 = 
\begin{vmatrix}
0 & 1 & 1 & 1\\
1 & 0 & c^2 & b^2\\
1 & c^2 & 0 & a^2\\
1 & b^2 & a^2 & 0
\end{vmatrix}.
$$

For this proof I prefer this form because it generalizes nicely to higher dimensions. This is the [Cayley-Menger determinant](https://en.wikipedia.org/wiki/Cayley%E2%80%93Menger_determinant) which gives a formula for the volume of an $n$-dimensional simplex in terms of its edge lengths. For our proof we will use the formula for the volume of the tetrahedron,

$$
288V^2= \begin{vmatrix}
0&1&1&1&1\\
1&0&d_{12}^{2}&d_{13}^{2}&d_{14}^{2}\\
1&d_{21}^{2}&0&d_{23}^{2}&d_{24}^{2}\\
1&d_{31}^{2}&d_{32}^{2}&0&d_{34}^{2}\\
1&d_{42}^{2}&d_{42}^{2}&d_{43}^{2}&0\\
\end{vmatrix}.
$$

# The proof

Begin with four pairwise-tangent spheres with radii $r_1$, $r_2$, $r_3$, and $r_4$. The centers of these spheres define a tetrahedron, and the Cayley-Menger determinant formula gives us the volume of that tetrahedron in terms of $d_{ij}=r_i+r_j$. After simplification, we are left with the tidy formula

$$
V^2 = \left(\frac{r_1r_2r_3r_4}{3}\right)^2\left[\left(\frac{1}{r_1}+\frac{1}{r_2}+\frac{1}{r_3}+\frac{1}{r_4}\right)^2 -2\left(\frac{1}{r_1^2}+\frac{1}{r_2^2}+\frac{1}{r_3^2}+\frac{1}{r_4^2}\right)\right].
$$

Now we just observe that if the four centers lie in a plane, their tetrahedron must have volume zero. If all the radii are nonzero, the term $r_1r_2r_3r_4$ cannot be zero, so that leaves

$$
\left(\frac{1}{r_1}+\frac{1}{r_2}+\frac{1}{r_3}+\frac{1}{r_4}\right)^2 -2\left(\frac{1}{r_1^2}+\frac{1}{r_2^2}+\frac{1}{r_3^2}+\frac{1}{r_4^2}\right)=0
$$

and the proof is complete.

# The details

I should really justify the volume formula I gave above, since it is not obvious and it is essential to the proof. It really is a "follow your nose" kind of derivation, starting with the Cayley-Menger determinant and performing straightforward simplifications. We use these common determinant simplification rules.

- Adding (or subtracting) one row or column to another does not change the determinant.
- Multiplying a row or column by a constant scales the determinant by that multiplicative factor.
- When $D$ is invertible, the block determinant $$\begin{vmatrix}A&B\\ C&D\end{vmatrix}$$ can be computed as $\left| D\right | \left| A - B D^{-1} C\right|$.

Since I write out the entire matrix on each line this fills a lot of space, but there are really very few steps involved. Without further ado, the algebra.

\begin{align*}
288V^2
&= \begin{vmatrix}
0&1&1&1&1\\
1&0&d_{12}^{2}&d_{13}^{2}&d_{14}^{2}\\
1&d_{21}^{2}&0&d_{23}^{2}&d_{24}^{2}\\
1&d_{31}^{2}&d_{32}^{2}&0&d_{34}^{2}\\
1&d_{42}^{2}&d_{42}^{2}&d_{43}^{2}&0\\
\end{vmatrix}\\
&= \begin{vmatrix}
0&1&1&1&1\\
1&0&(r_1+r_2)^2&(r_1+r_3)^2&(r_1+r_4)^2\\
1&(r_2+r_1)^2&0&(r_2+r_3)^2&(r_2+r_4)^2\\
1&(r_3+r_1)^2&(r_3+r_2)^2&0&(r_3+r_4)^2\\
1&(r_4+r_1)^2&(r_4+r_2)^2&(r_4+r_3)^2&0\\
\end{vmatrix}\\
\frac{288V^2}{(r_1r_2r_3r_4)^4}&= \begin{vmatrix}
0&\frac{1}{r_1^2}&\frac{1}{r_2^2}&\frac{1}{r_3^2}&\frac{1}{r_4^2}\\
\frac{1}{r_1^2}&0&(\frac{1}{r_1}+\frac{1}{r_2})^2&(\frac{1}{r_1}+\frac{1}{r_3})^2&(\frac{1}{r_1}+\frac{1}{r_4})^2\\
\frac{1}{r_2^2}&(\frac{1}{r_2}+\frac{1}{r_1})^2&0&(\frac{1}{r_2}+\frac{1}{r_3})^2&(\frac{1}{r_2}+\frac{1}{r_4})^2\\
\frac{1}{r_3^2}&(\frac{1}{r_3}+\frac{1}{r_1})^2&(\frac{1}{r_3}+\frac{1}{r_2})^2&0&(\frac{1}{r_3}+\frac{1}{r_4})^2\\
\frac{1}{r_4^2}&(\frac{1}{r_4}+\frac{1}{r_1})^2&(\frac{1}{r_4}+\frac{1}{r_2})^2&(\frac{1}{r_4}+\frac{1}{r_3})^2&0\\
\end{vmatrix}\\
&= \begin{vmatrix}
0&\frac{1}{r_1^2}&\frac{1}{r_2^2}&\frac{1}{r_3^2}&\frac{1}{r_4^2}\\
\frac{1}{r_1^2}&0&\frac{1}{r_1^2}+\frac{2}{r_1r_2}+\frac{1}{r_2^2}&\frac{1}{r_1^2}+\frac{2}{r_1r_3}+\frac{1}{r_3^2}&\frac{1}{r_1^2}+\frac{2}{r_1r_4}+\frac{1}{r_4^2}\\
\frac{1}{r_2^2}&\frac{1}{r_2^2}+\frac{2}{r_2r_1}+\frac{1}{r_1^2}&0&\frac{1}{r_2^2}+\frac{2}{r_2r_3}+\frac{1}{r_3^2}&\frac{1}{r_2^2}+\frac{2}{r_2r_4}+\frac{1}{r_4^2}\\
\frac{1}{r_3^2}&\frac{1}{r_3^2}+\frac{2}{r_3r_1}+\frac{1}{r_1^2}&\frac{1}{r_3^2}+\frac{2}{r_3r_2}+\frac{1}{r_2^2}&0&\frac{1}{r_3^2}+\frac{2}{r_3r_4}+\frac{1}{r_4^2}\\
\frac{1}{r_4^2}&\frac{1}{r_4^2}+\frac{2}{r_4r_1}+\frac{1}{r_1^2}&\frac{1}{r_4^2}+\frac{2}{r_4r_2}+\frac{1}{r_2^2}&\frac{1}{r_4^2}+\frac{2}{r_4r_3}+\frac{1}{r_3^2}&0\\
\end{vmatrix}\\
&= \begin{vmatrix}
0&\frac{1}{r_1^2}&\frac{1}{r_2^2}&\frac{1}{r_3^2}&\frac{1}{r_4^2}\\
\frac{1}{r_1^2}&\frac{-2}{r_1^2}&\frac{2}{r_1r_2}&\frac{2}{r_1r_3}&\frac{2}{r_1r_4}\\
\frac{1}{r_2^2}&\frac{2}{r_2r_1}&\frac{-2}{r_2^2}&\frac{2}{r_2r_3}&\frac{2}{r_2r_4}\\
\frac{1}{r_3^2}&\frac{2}{r_3r_1}&\frac{2}{r_3r_2}&\frac{-2}{r_3^2}&\frac{2}{r_3r_4}\\
\frac{1}{r_4^2}&\frac{2}{r_4r_1}&\frac{2}{r_4r_2}&\frac{2}{r_4r_3}&\frac{-2}{r_4^2}\\
\end{vmatrix}\\
\frac{288V^2}{(r_1r_2r_3r_4)^2}&= \begin{vmatrix}
0&\frac{1}{r_1}&\frac{1}{r_2}&\frac{1}{r_3}&\frac{1}{r_4}\\
\frac{1}{r_1}&-2&2&2&2\\
\frac{1}{r_2}&2&-2&2&2\\
\frac{1}{r_3}&2&2&-2&2\\
\frac{1}{r_4}&2&2&2&-2
\end{vmatrix}\\
\frac{288V^2}{8(r_1r_2r_3r_4)^2}&= \begin{vmatrix}
0&\frac{1}{r_1}&\frac{1}{r_2}&\frac{1}{r_3}&\frac{1}{r_4}\\
\frac{1}{r_1}&-1&1&1&1\\
\frac{1}{r_2}&1&-1&1&1\\
\frac{1}{r_3}&1&1&-1&1\\
\frac{1}{r_4}&1&1&1&-1
\end{vmatrix}\\
&=-\begin{vmatrix}
-1&1&1&1\\
1&-1&1&1\\
1&1&-1&1\\
1&1&1&-1
\end{vmatrix}
\begin{bmatrix} 1/r_1 \\ 1/r_2 \\ 1/r_3 \\ 1/r_4 \end{bmatrix}^T
\begin{bmatrix}
-1&1&1&1\\
1&-1&1&1\\
1&1&-1&1\\
1&1&1&-1
\end{bmatrix}^{-1}
\begin{bmatrix} 1/r_1 \\ 1/r_2 \\ 1/r_3 \\ 1/r_4 \end{bmatrix}
\end{align*}

Notice the matrix 
$$
D=\begin{bmatrix}
-1&1&1&1\\
1&-1&1&1\\
1&1&-1&1\\
1&1&1&-1
\end{bmatrix}
$$
satisfies $D^2=4I$. Thus, $D^{-1} = \frac{1}{4}D$. We can also compute $|D| = -16$. Just a couple more lines and we are done with the algebra.

\begin{align*}
\frac{288V^2}{32(r_1r_2r_3r_4)^2}
&=
\begin{bmatrix} 1/r_1 \\ 1/r_2 \\ 1/r_3 \\ 1/r_4 \end{bmatrix}^T
\begin{bmatrix}
-1&1&1&1\\
1&-1&1&1\\
1&1&-1&1\\
1&1&1&-1
\end{bmatrix}
\begin{bmatrix} 1/r_1 \\ 1/r_2 \\ 1/r_3 \\ 1/r_4 \end{bmatrix}\\
\frac{9V^2}{(r_1r_2r_3r_4)^2}
&=
\begin{bmatrix} 1/r_1 \\ 1/r_2 \\ 1/r_3 \\ 1/r_4 \end{bmatrix}^T
\begin{bmatrix}
1&1&1&1\\
1&1&1&1\\
1&1&1&1\\
1&1&1&1
\end{bmatrix}
\begin{bmatrix} 1/r_1 \\ 1/r_2 \\ 1/r_3 \\ 1/r_4 \end{bmatrix}
-2\begin{bmatrix} 1/r_1 \\ 1/r_2 \\ 1/r_3 \\ 1/r_4 \end{bmatrix}^T
\begin{bmatrix}
1&0&0&0\\
0&1&0&0\\
0&0&1&0\\
0&0&0&1
\end{bmatrix}
\begin{bmatrix} 1/r_1 \\ 1/r_2 \\ 1/r_3 \\ 1/r_4 \end{bmatrix}\\
&=\left(\frac{1}{r_1}+\frac{1}{r_2}+\frac{1}{r_3}+\frac{1}{r_4}\right)^2 -2\left(\frac{1}{r_1^2}+\frac{1}{r_2^2}+\frac{1}{r_3^2}+\frac{1}{r_4^2}\right)
\end{align*}

