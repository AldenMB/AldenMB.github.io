Title: A Cubic Spline for Animation
Date: Sept 9 2022
Category: Blog
Tags: Interpolation, Neopixels, Animation
Summary: Developing a simple linear program for animating an LED string

I am designing [animations for a string of addressable LEDs](https://github.com/AldenMB/addressable_led_patterns). Deterministic patterns generally have a nice regularity to them, but their visual interest diminishes after a while. Random patterns can have a bit more visual interest, since they are different every time. If we just assign a random value to each pixel, the result is very busy, loud, disjointed. It would be better to have something changing in a random way, but with a smooth, regular structure so that the eye can actually take it all in.

One nice way to get a smooth, organic-feeling function is to use a polynomial spline. We can specify values to achieve at certain times (in animation these are called "key frames") and use a polynomial to interpolate between them. By choosing the spline carefully, we can get a motion which is not only continuous, but smooth in both time and space. It will have no jumps, and no times or places where the color suddenly starts changing in a different way. In this post we will walk through a procedure for generating those splines using linear algebra.

# The setting

We have a one-dimensional string of LEDs. We can specify the location of each LED by a number on an interval. To make the numbers work out nice, let's put them on the interval $[0, 3]$. For symmetry, let's describe time in the same way, as an interval $[0, 3]$. For reasons which will become clear, we will focus on the time subinterval $[1, 2]$. We will put the key frames at $t=0$, $t=1$, $t=2$, and $t=3$.  We will specify each key frame by the values at $s=0$, $s=1$, $s=2$, and $s=3$. That is, we begin with a function defined on $\{0, 1, 2, 3\}\times\{0, 1, 2, 3\}$, and we wish to extend it to a function on $[1, 2]\times[0, 3]$.

Why use this specific number of key values? I want to make a cubic polynomial, which we can write as $f(t, s) = \sum_{i, j=0}^3 A_{ij}t^i s^j$. This has sixteen degrees of freedom. By specifying the function at sixteen points, we can make a system which is neither over-constrained nor under-constrained.

Why go for a cubic function? I want to get a curve which is smooth in time and space. On a given interval, I want it to hit the key frames, but I also want it to be smooth at both ends. That makes four constraints, so a third degree polynomial is just right.

Why look at times between 1 and 2? I want to think of time as starting at 1, and proceeding to 2. When the time hits 2, we can extend $f$ by defining $f(t, s) = g(t-1, s)$ for $t\in\{1\}\cup[2,3]$. In this way, $g$ has the same form as $f$, with values of $g(t)$ specified for $t\in \{0, 1, 2\}$. By dropping the values of $f$ at $t=0$ and making new values for $g$ at $t=3$, the program repeats with $g$ taking the role of $f$.

# The clever bit

When we restrict $f$ to $t=2$, we get a polynomial in $s$. Since it interpolates four values at $s=0, 1, 2, 3$, it is completely specified. Since $g$ restricted to $t=1$ is also a cubic interpolant, we can see that $f$ is continuous not only at $\{2\}\times \{0, 1, 2, 3\}$ but on all of $\{2\}\times [0, 3]$. If we are clever about how we specify $f$, we can do even better. Notice that $f_t$ is also a cubic polynomial in $s$ when restricted to $t=2$. If we can specify $f_t(2, s)=g_t(1, s)$ then we can make $f'$ continuous as well!

How to do this? Since $f$ is never directly evaluated at $t=3$, we will use the funciton values there to instead specify $f_t$ at $t=2$. That is, we will enforce $f_t(2, s) = (1-c) (f(3, s)-f(1, s))/2$, where $c$ is a "tension" term allowing us to tweak the behavior of the interpolant. By assigning the same rule for $g$, that is, $g_t(1, s) = (1-c)(f(3, s)-f(1, s))/2$, we get a function which is continuous and smooth.

# Computing it

We began by writing $f(t, s)=\sum_{i, j=0}^3 A_{ij}t^i s^j$. We can write this as a matrix product, 
$$
f(t, s) = 
\begin{bmatrix}
1 \\ t \\ t^2 \\ t^3
\end{bmatrix}^T
A
\begin{bmatrix}
1 \\ s \\ s^2 \\ s^3
\end{bmatrix}.
$$
to keep things tidy, let's define $P_x = [1, x, x^2, x^3]^T$. Then we can write this as $f(t, s)=P_t^T A P_s$. The goal then is to specify the matrix $A$, in terms of the data, the key-frame values of $f$ on $\{0, 1, 2, 3\}^2$. Write $F=[f(i, j)]_{i, j=0}^3$, so we are looking for how to write $A$ in terms of $F$.

## Continuity

We use the values at $t=1, 2$ to enforce the continuity of the spline. This is straightforward: we want $P_1^TAP_s = F_{1s}$ for $s=0, 1, 2, 3$. Writing $P_S = [P_0P_1P_2P_3]$ we can write this more compactly as $P_1^TAP_S = F_1$. Similarly, we want $P_2^TAP_S = F_2$. Together, these give 

$$
[P_1, P_2]^T A P_S = \begin{bmatrix} 0 & 1 & 0 & 0 \\ 0 & 0 & 1 & 0 \end{bmatrix} F.
$$

Not enough to solve for $A$ yet, but we have not used smoothness yet.

## Smoothness

Differentiation yields

$$
f_t(t, s) = 
\begin{bmatrix}
0 \\ 1 \\ 2t \\ 3t^2
\end{bmatrix}^T
A
\begin{bmatrix}
1 \\ s \\ s^2 \\ s^3
\end{bmatrix}.
$$

Write $D_x=\frac{d}{dx} P_x$, so we can write this as $f_t = D_t^T A P_s$. We want $f(1, s) = \frac{1-c}{2}(f(2, s) - f(0, s))$. As a matrix equation, this becomes

$$
D_1^T A P_S = \frac{1-c}{2} \begin{bmatrix}-1 & 0 & 1 & 0\end{bmatrix}F.
$$

Repeating for the $t=2$ case yields

$$
[D_1, D_2]^T A P_S = \frac{1-c}{2}\begin{bmatrix}-1&0&1&0\\0&-1&0&1\end{bmatrix}F.
$$

Writing it this way shows how to combine with the continuity equation,

$$
[P_1, P_2, D_1, D_2]^T A P_S = \frac{1}{2}\left(
\begin{bmatrix}
0 & 2 & 0 & 0\\
0 & 0 & 2 & 0\\
-1& 0 & 1 & 0\\
0 &-1 & 0 & 1
\end{bmatrix}
+c\begin{bmatrix}
0 & 0 & 0 & 0\\
0 & 0 & 0 & 0\\
1 & 0 &-1 & 0\\
0 & 1 & 0 &-1
\end{bmatrix}
\right)F.
$$

## Solving for $A$

Now all the matrices are square, so we are in business. We have enough to solve for $A$. Writing $U=[P_1,P_2,D_1,D_2]^T$ we find
$$
U^{-1}
=
\begin{bmatrix}-4 & 5 & -4 & -2\\12 & -12 & 8 & 5\\-9 & 9 & -5 & -4\\2 & -2 & 1 & 1\end{bmatrix}.
$$
We can also compute 
$$
P_S^{-1} = \begin{bmatrix}1 & 1 & 1 & 1\\0 & 1 & 2 & 3\\0 & 1 & 4 & 9\\0 & 1 & 8 & 27\end{bmatrix}^{-1}
=\frac{1}{6} \begin{bmatrix}6 & -11 & 6 & -1\\0 & 18 & -15 & 3\\0 & -9 & 12 & -3\\0 & 2 & -3 & 1\end{bmatrix}.
$$
Multiplying on both sides yields

$$
A = \frac{1}{12} \left(\begin{bmatrix}4 & -6 & 6 & -2\\-8 & 19 & -16 & 5\\5 & -14 & 13 & -4\\-1 & 3 & -3 & 1\end{bmatrix} + c \begin{bmatrix}-4 & -2 & 4 & 2\\8 & 5 & -8 & -5\\-5 & -4 & 5 & 4\\1 & 1 & -1 & -1\end{bmatrix} \right)
F \begin{bmatrix}-4 & 5 & -4 & -2\\12 & -12 & 8 & 5\\-9 & 9 & -5 & -4\\2 & -2 & 1 & 1\end{bmatrix}.
$$

## Applying the formula

Now that we have a formula for $f(t, s)=P_t^TAP_s$, we will want to apply it to our light string, which is discrete. Choose some discrete times $T = [1, 1+\Delta t, 1+2\Delta t, \dots, 2]$ and string positions $S=[0,\Delta s, 2\Delta s, ..., 3]$. Form them into polynomials $P_T$ and $P_S$ as before, then multiply them on the outside in advance; the coefficient matrices we derived above will not change, only F will change. This can save us a lot of multiplication, since much of it can be done in advance.

# Conclusion

I won't belabor this any further here. You can see how this algorithm is implemented [in the repository I have shared](https://github.com/AldenMB/addressable_led_patterns). I think this shows how linear algebra allows us to express some pretty complicated formulas quite compactly, which lets us think about more-complicated things than would otherwise be possible.