Title: Solution to a puzzle on the incircle of a triangle
Date: Apr 9 2023
Category: Blog
Tags: Geometry, Puzzles
Summary: Solution to a geometry puzzle

Some times I hang around the [math help subreddit](https://www.reddit.com/r/MathHelp/). Most of the people posting there are doing some standard homework problem from algebra or calculus, and they get help right away. Every so often there will be a probability question, and those people get help within a day or so. My favorite to see are the geometry questions. Those questions don't always get solved, since they tend to be trickier. That's [where I found](https://www.reddit.com/r/MathHelp/comments/12cgipu/geometry_question/) the following puzzle.

# The puzzle

Begin with a general triangle $\triangle ABC$. Construct the incenter $I$, altitude $AH$, and $M$ the midpoint of $BC$. Define $K$ to be the intersection of the lines $AH$ and $MI$. Show that $\overline{AK}$ is equal to the inradius $r$ of the triangle.

<iframe src="https://www.geogebra.org/calculator/sfdk3mqv?embed" width="600" height="400" allowfullscreen style="border: 1px solid #e4e4e4;border-radius: 4px;" frameborder="0"></iframe>

Just for convenience, let's also define $D$ to be the point on $BC$ which touches the incircle, so that $\overline{ID}=r$.

This puzzle had me stumped for a few days. I did eventually get a solution which is not too messy, detailed below, thought I suspect there is a nicer solution using more geometry. I enjoyed solving this, and I hope you will to. If you would like to solve it yourself, stop here. Otherwise, read on.

# The solution strategy

The main idea is to consider the area of the triangle. We are given an altitude, after all, and altitudes are good for finding areas. We will compute the area of $\triangle ABC$ in two different ways. First, using the given altitude, the area must be $\frac{1}{2}\overline{AH}\overline{BC}$. Second, by partitioning $\triangle ABC$ along its angle bisectors (into $\triangle ABI$, $\triangle BCI$, and $\triangle CAI$) we find another formula for the area, $\frac{1}{2}r(\overline{AB}+\overline{BC}+\overline{AC})$. That is,

\begin{equation}
r(\overline{AB}+\overline{BC}+\overline{AC})=\overline{AH}\overline{BC}.\tag{I}
\end{equation}


We will use one other fact about the incenter, which may not be immediately obvious.

\begin{equation}
\overline{AB}+\overline{CD} = \overline{AC}+\overline{DB}.\tag{II}
\end{equation}

Here is a picture-proof of that theorem. Connecting the incenter to each side along a radius, we split the trianlge into three kites. Dividing each in half, we get three pairs of congruent right triangles, all with the same altitude $r$. Equation $\text{(II)}$ simply follows from grouping those triangles and considering their bases.

<iframe src="https://www.geogebra.org/calculator/axrqzdyb?embed" width="600" height="400" allowfullscreen style="border: 1px solid #e4e4e4;border-radius: 4px;" frameborder="0"></iframe>

Besides that, we will only use Pythagoras, 
\begin{equation}
\overline{AB}^2=\overline{AH}^2+\overline{HB}^2,\qquad \overline{AC}^2=\overline{AH}^2+\overline{CH}^2,\tag{III}
\end{equation}
the definition of midpoint,
\begin{equation}
\overline{CM}=\overline{BM},\tag{IV}
\end{equation}
and similar triangles,
\begin{equation}
r\overline{MH}=\overline{KH}\overline{MD}.\tag{V}
\end{equation}

If you would like to try it for yourself, have a go at manipulating the above formulas to get the answer. Otherwise read on.

# The arithmetic

Depending on the relative positions of $A$, $B$, and $C$, the lengths will add up differently. To make it simpler to follow, we will assume that (as in the diagram above) $H$ lies between $M$ and $B$. The strategy will work in any case, but this will make a world of difference for readability.

Let's begin at $\text{(III)}$, subtracting the two equations to cancel the $\overline{AH}$ term.
\begin{align*}
\overline{AC}^2-\overline{AB}^2
&=\overline{CH}^2-\overline{HB}^2\\
&=(\overline{CH}+\overline{HB})(\overline{CH}-\overline{HB})\\
&=\overline{BC}(\overline{CM}+\overline{MH}-\overline{HB})\\
&=\overline{BC}(\overline{BM}+\overline{MH}-\overline{HB})\\
&=2\overline{BC}\overline{MH}.\tag{VI}
\end{align*}

Inspired by the difference-of-squares formula, let's manipulate $\text{(II)}$ similarly.
\begin{align*}
\overline{AC}-\overline{AB}
&= \overline{CD}-\overline{DB}\\
&= \overline{CM}+\overline{MD} -\overline{DB}\\
&= \overline{BM}+\overline{MD} -\overline{DB}\\
&= 2\overline{MD}.\tag{VII}
\end{align*}

Do the same with $\text(I)$,
\begin{equation}
r(\overline{AC}+\overline{AB})=\overline{BC}(\overline{AH}-r).\tag{VIII}
\end{equation}

Difference of squares takes us home, and $\text{(V)}$ lays the problem to rest.
\begin{align*}
r(\overline{AC}^2-\overline{AB}^2) &= r(\overline{AC}+\overline{AB})(\overline{AC}-\overline{AB})\\
2r\overline{BC}\overline{MH} &= \overline{BC}(\overline{AH}-r)2\overline{MD}\\
r\overline{MH} &= (\overline{AH}-r)\overline{MD}\\
\overline{KH}\overline{MD} &= (\overline{AH}-r)\overline{MD}\\
r&=\overline{AH}-\overline{KH}\\
&=\overline{AK}.
\end{align*}

# Conclusion

As mentioned above, this solution is based on areas, because we are given altitudes. It still feels quite algebra-heavy for a geometry proof. In general, $H$ could lie to the left of $C$, between $C$ and $M$, between $M$ and $B$, or to the right of $B$. Giving all the details of each case would be tiresome, but would make this proof fully rigorous. I can't quite shake the feeling that there must be a tidyer way about this problem, using less algebra and more geometry. For now, I leave it here. I hope you enjoyed this puzzle as much as I did.