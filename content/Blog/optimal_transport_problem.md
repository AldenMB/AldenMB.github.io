Title: A Problem in Optimal Transport
Date: Sept 2 2022
Category: Blog
Tags: Optimization, Convex Functions
Summary: We show why convex cost functions lead to certain types of solutions to optimal transport problems, using a simple example.

I like math classes. I don't like turning in math homework. Lucky for me, I am far enough along in my math education that people mostly don't assign me homework. On the other hand, solving problems is the best way anyone has found so far of learning math. I am taking a course in optimal transport theory, and so I have made some homework for myself.

In one dimension, the optimal transport problem is sometimes quite easy to solve. So long as the cost function $c(x,y)$ can be written as $\phi(|x-y|)$ where $\phi$ is convex and increasing, then there is an optimal transport plan $T$ and it is monotone, in the sense that if $x_1<x_2$ then $T(x_1)\leq T(x_2)$. Why is this? I feel we can get to the heart of it by discarding concerns about complicated distributions and sigma algebras. Let's focus on the case where we just have two point masses to move to two targets. That is, our source distribution is $\delta(x_1)+\delta(x_2)$ and the target is $\delta(y_1)+\delta(y_2)$. We will take $x_1<x_2$ and $y_1<y_2$. There are two transport plans to consider.
\begin{align*}
T_\parallel&\begin{cases} x_1 \mapsto y_1 \\ x_2\mapsto y_2 \end{cases} \\
T_\perp&\begin{cases} x_1 \mapsto y_2 \\ x_2\mapsto y_1 \end{cases}
\end{align*}
In a sense the plan $T_\perp$ crosses itself, while $T_\parallel$ does not. Our claim is that, given $\phi$ is convex and nondecreasing, $c(T_\parallel)\leq c(T_\perp)$. We will prove this together, paying special attention to where we use each of the conditions on $\phi$. That is, we will show that
$$
\phi(|y_1-x_1|)+\phi(|y_2-x_2|) \leq \phi(|y_1-x_2|)+\phi(|y_2-x_1|).\tag{$*$}
$$

# The proof

Inequality $(*)$ is symmetric in terms of $x$ and $y$, so we can take $x_1<y_1$ without loss of generality. Let's treat two cases, either $x_2>y_2$ or $x_2<y_2$.

## When $x_2>y_2$

Our assumptions together tell us $x_1\leq y_1\leq y_2 \leq x_2$. Then our goal $(*)$ can be written as
$$
\phi(y_1-x_1)+\phi(x_2-y_2) \leq \phi(y_2-x_1)+\phi(x_2-y_1).
$$
This case is solved by the nondecreasing assumption on $\phi$, since $\phi(y_1-x_1)\leq \phi(y_2-x_1)$ and $\phi(x_2-y_2)\leq \phi(x_2-y_1)$. We do not require convexity at all in this case.

## When $x_2<y_2$

We can rewrite our goal $(*)$ as 
$$
\phi(y_1-x_1)+\phi(y_2-x_2) \leq \phi(y_2-x_1) + \phi(|y_1-x_2|).
$$
By repeatedly applying $x_1<x_2$ and $y_1<y_2$ we find $y_1-x_2\leq y_1-x_1\leq y_2-x_1$ and $y_1-x_2\leq y_2-x_2 \leq y_2-x_1$. By convexity, we have
\begin{align*}
\phi(y_1-x_1)&\leq \frac{y_2-y_1}{y_2-y_1+x_2-x_1}\phi(y_2-x_1)+\frac{x_2-x_1}{y_2-y_1+x_2-x_1}\phi(y_1-x_2),\\
\phi(y_2-x_2)&\leq \frac{x_2-x_1}{y_2-y_1+x_2-x_1}\phi(y_2-x_1)+\frac{y_2-y_1}{y_2-y_1+x_2-x_1}\phi(y_1-x_2).
\end{align*}
Adding these yields
$$
\phi(y_1-x_1)+\phi(y_2-x_2) \leq \phi(y_2-x_1) + \phi(y_1-x_2).
$$
Finally, the nondecreasing assumption tells us $\phi(y_1-x_2)\leq \phi(|y_1-x_2|)$.

# Conclusion
The $x_2>y_2$ case above relied only on the nondecreasing assumption. Indeed, it seems to me that it is both necessary and sufficient in that case. That's the case where both distances are reduced by changing from $T_\perp$ to $T_\parallel$. In the other case, $x_2<y_2$, we used convexity because $|x_1-T(x_1)|$ decreased while $|x_2-T(x_2)|$ increased. Convexity allows us to express the "diminishing return" property of a cost, where it's at least twice as hard to move a mass twice as far. In this case the trade was worth it, because $x_1$ had farther to go.

None of this is new stuff. Looking ahead, proposition 4.5 of Luigi Ambrosio's book treats a very similar question, from a slightly less elementary perspective. However, I do think this example is illustrative. It shows exactly what aspect of the cost function we are taking advantage of, when we use the convexity property.