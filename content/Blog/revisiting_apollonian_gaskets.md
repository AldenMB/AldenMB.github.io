Title: Revisiting Apollonian gaskets
Date: Aug 6 2022
Category: Blog
Tags: number theory, Apollo, Apollonian circle packings 
Summary: A better way of generating Apollonian gaskets.

Last summer I designed [Apollo](/Apollo), a tool for making Apollonian gaskets. Now I am a year older and a year wiser, so I think it's worth revisiting it. There are a few great improvements we can make, particularly when it comes to making integral packings, i.e. packings where the bend of every circle is an integer.


# Computing bends to start from

## No primitive bends

There are basically two places where the code can be improved. First, the algorithm I use to find the primitive bends for a packing could be better. In particular, there are cases where it gets into an infinite loop, due to the gasket being in fact unbounded. The algorithm I use starts with three bends and finds a fourth using the quadratic formula. Then, by placing the bends in increasing order and using the mapping
$$
(w, x, y, z) \mapsto (w, x, y, 2(w+x+y)-z)
$$
we make the largest bend smaller. If this makes the largest bend bigger instead, we are done, we found the primitive bends. Otherwise we sort the bends to find the new largest bend and repeat.

The problem with this strategy is that for some gaskets, the bend can decrease indefinitely toward zero without going negative. In those cases this strategy will not terminate. Here is an example. To make things simple, let's take $w=0$, and take the other bends to be in a geometric progression $(x, y, z) = (x, \alpha x, \alpha^2 x)$. By Descartes's theorem, $\alpha$ must satisfy the equation 
$$
2(1+\alpha^2+\alpha^4) = (1+\alpha+\alpha^2)^2.
$$
At first this may look nasty, but it gets nicer if we do a backwards sort of completing the square, I suppose you could say completing the difference-of-squares.
\begin{align*}
\alpha^4+\alpha^2+1
&= (\alpha^4+2\alpha^2+1)-\alpha^2\\
&= (\alpha^2+1)^2-\alpha^2\\
&= (\alpha^2+\alpha+1)(\alpha^2-\alpha+1)
\end{align*}
So, we see a common factor in both sides of the equation. We can factor it out as 
$$
(\alpha^2+\alpha+1)(\alpha^2-3\alpha+1)=0.
$$
This has two real roots, the solutions to $\alpha^2-3\alpha+1=0$. For our purposes I prefer to write this as $\alpha+\frac{1}{\alpha} = 3$, which emphasizes that the two solutions are reciprocal to one another. Taking $\alpha$ to be the larger of these two roots ($\alpha=(3+\sqrt{5})/2$ if you are curious) puts our original bends in increasing order.

If we apply our mirroring strategy to $(0, x, \alpha x, \alpha^2 x)$ we get rid of $\alpha^2 x$ and replace it with 
\begin{align*}
2(x+\alpha x)-\alpha^2 x 
&= 2x+2\alpha x -(3\alpha-1) x\\
&= (3-\alpha) x\\
&= \frac{1}{\alpha} x.
\end{align*}
Sorting the bends gives us $(0, \frac{1}{\alpha} x, x, \alpha x)$. We see that for this configuration of bends the mirroring lets the bends decrease indefinitely toward zero, dividing their magnitudes by $\alpha$ at each stage.

This can never happen if the first four bends are integers since no sequence of positive integers can decrease forever. The method specified above fails precisely because some of the time we don't get integer bends. A better solution would be to treat the cases separately, i.e. figure out a plan to deal with large regions and just plot any three bends specified, without first reducing the bends.

## A list of good bends

The reason I previously included the bend-reducing algorithm was as a way to generate integral gaskets. Usually, if I care about the exact bend of a gasket, it's because it's an integral packing. Why not simply provide a list of all the integral packings?

In [this paper from 2003](https://arxiv.org/abs/math/0009113) the authors provide a way of describing all the primitive integral packings in their theorem 4.1. They give a one-to-one correspondence between primitive packings of the form $(a, b, c, d)$ and integer solutions to the equation $x^2+m^2=d_1d_2$ satisfying $x<0\leq 2m\leq d_1 \leq d_2$ and $\gcd(x, d_1, d_2)=1$.

\begin{align*}
a&=x\\
b&=d_1-x\\
c&=d_2-x\\
d&=-2m+d_1+d_2-x
\end{align*}

 I want these grouped by their outer bend, so I will fix $a=x=-n$. As it turns out, for each fixed $n$ there are not too many candidates for $m$, and we can check them all provided $n$ isn't too big.

Since we want $2m\leq d_1$ and $2m\leq d_2$ it follows that $4m^2\leq d_1d_2$ and so $3m^2\leq d_1d_2-m^2=n^2$. Thus, we only need to check $m<n/\sqrt{3}$. For each $m$, we can just try all the numbers $d_1$ between $2m$ and $\sqrt{n^2+m^2}$ to see if they divide $n^2+m^2$. If so that gives us $d_2$, so we can just check if $\gcd(n, d1, d2)=1$. Here's some Python code to carry out this procedure.


```python
import math


def get_primitive_bends(n):
    if n == 0:
        yield 0, 0, 1, 1
        return
    for m in range(math.ceil(n / math.sqrt(3))):
        s = m**2 + n**2
        for d1 in range(max(2 * m, 1), math.floor(math.sqrt(s)) + 1):
            d2, remainder = divmod(s, d1)
            if remainder == 0 and math.gcd(n, d1, d2) == 1:
                yield -n, d1 + n, d2 + n, d1 + d2 + n - 2 * m


for n in range(10):
    for bend in get_primitive_bends(n):
        print(bend)

assert [b for n in range(10) for b in get_primitive_bends(n)] == true_bends
```

This gives us exactly [the list on Wikipedia](https://en.wikipedia.org/wiki/Apollonian_gasket#Integral_Apollonian_circle_packings).

```python
(0, 0, 1, 1)
(-1, 2, 2, 3)
(-2, 3, 6, 7)
(-3, 4, 12, 13)
(-3, 5, 8, 8)
(-4, 5, 20, 21)
(-4, 8, 9, 9)
(-5, 6, 30, 31)
(-5, 7, 18, 18)
(-6, 7, 42, 43)
(-6, 10, 15, 19)
(-6, 11, 14, 15)
(-7, 8, 56, 57)
(-7, 9, 32, 32)
(-7, 12, 17, 20)
(-8, 9, 72, 73)
(-8, 13, 21, 24)
(-8, 12, 25, 25)
(-9, 10, 90, 91)
(-9, 11, 50, 50)
(-9, 14, 26, 27)
(-9, 18, 19, 22)
```

We could certainly get more clever about how we find $d_1$ and $d_2$, though trial division works well for small $n$. An example speedup is to notice $d_1d_2=m^2+n^2$ is the sum of two squares. By the aptly-named [sum of two squares theorem](https://en.wikipedia.org/wiki/Sum_of_two_squares_theorem), we know any prime factors of $m^2+n^2$ which are congruent to $3$ mod $4$ must have an even exponent -- for example, if $m^2+n^2$ is divisible by $7$ then it must also be divisible by $49$. We can use that to find the factorization quicker, possibly requiring many fewer trial divisions.

# Using integral bend-centers

In [this other article](https://arxiv.org/abs/math/0010302v5), the authors show that every integral packing can be translated and rotated to make it strongly integral, i.e. with integer bend-centers. They do this by embedding every integral packing into a single integral superpacking, and rotating/translating that integral superpacking to get a strongly integral superpacking.

The benefits of having integer bend-centers should be obvious. Not only is it generally faster to do arithmetic on integers, we also no longer have to worry about floating point errors creeping in as we delve deeper. We could use this to make an interactive tool which lets you zoom in as far as you like, without fear of drifting off course.

In that paper they do not give an algorithm explicitly for constructing a strongly integral packing for a given integral packing. However, we can adapt a couple of their theorems to create a strategy. That's what I'll describe here.

Their theorem 4.2 is proved by creating a superapollonian group action which takes a given Descartes configuration to $(0, 0, g, g)$. If we start with a primitive configuration, then we can take $g=1$. What we will do is apply their proposed strategy, saving the group action -- specifically, its representation as a 4x4 orthogonal matrix. Taking the transpose gives us the inverse action.

We can then apply that action to a strongly integral packing of $(0, 0, 1, 1)$. For example, if we apply it to 
$$
\begin{bmatrix}
0 & 0 & 1\\
0 & 0 &-1\\
1 & 1 & 0\\
1 &-1 & 0
\end{bmatrix}
$$
then we will get the embedding of our desired configuration of bends in the standard strongly integral superpacking. What's more, the location in the plane will be aligned with the superpacking -- we can trivially shift it back to the origin for plotting if we wish, or we can make a neat drawing of the whole superpacking with the desired circle indicated.

# Where this will go

I don't personally have too much interest in making more drawings of gaskets. The ones I made are pretty enough, and Apollo does well enough at creating them. I might patch it to use the list of gaskets above. If I do make a new version, I think I would want it to be a tool for exploring the Superapollonian packing. Something where you can scroll through in real time, or pinch to zoom on a touchscreen. Have a side panel to highlight specific integral packings.