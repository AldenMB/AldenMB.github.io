Title: Linear Regression is Simpler Than You Think
Date: March 19 2026
Category: Blog
Tags: Statistics, Teaching
Summary: A brief explanation of the linear regression equation

Everywhere I look, I see exposition of linear regression with some major mathematical errors. I think this is a shame, because the math behind it is quite simple. Here, I intend to summarize a few key points, and give you some proof that are as simple as possible (and no simpler). We don't need any calculus here, but the results apply equally well whether we are talking about discrete or continuous random variables. There is nothing here which you won't find buried in a mathematical statistics textbook, or in the appendix of a more technical introductory statistics textbook. I hope that this exposition will be clear enough with only a basic understanding of probability theory. This is a rough draft, but I will put it up anyway, because I don't plan to revisit it any time soon.

# Why the regression formula produces the least square error

Given two random variables $X$ and $Y$, we write the linear estimator for $Y$ as $\hat{Y}=\beta_0+\beta_1 X$, where $\beta_0$ and $\beta_1$ are some yet-to-bet-determined constants. The error of the estimator is $Y-\hat{Y}$. The mean square error is therefore $E[(Y-\hat{Y})^2]$. Here is a theorem: this error is minimized when $\beta_1 =Cov[x, y]/Var(X)$ and $\beta_0=E[Y]-\beta_1 E[X]$. I recently removed from Wikipedia the false claim that this result requires some special distribution for $X$ and $Y$. In fact, all that we require is that $X$ is not constant (so that $Var(X)$ is not zero) and that $X$ and $Y$ are both have a variance.

## Lemmas

$Var(A)=E[A^2]-E[A]^2$

$Var(A+c)=Var(A)$

$Var(A+B)=Var(A)+Var(B)+2Cov(A, B)$

## Proof

Here is the outline. We can show, just through the arithmetic of random variables, that 

\begin{align*}
E[(Y-\hat{Y})^2] &= Var(Y)-\frac{(Cov(X, Y))^2}{Var(X)} +\frac{(\beta_1Var(X)-Cov(X, Y))^2}{Var(X)}+E[Y-\hat{Y}]^2.
\end{align*}

By choosing $\beta_1=Cov(X, Y)/Var(X)$, we can make the term $(\beta_1Var(X)-Cov(X, Y))^2/Var(X)$ exactly zero. By then choosing $\beta_0=E[Y]-\beta_1 E[X]$ we can make $E[Y-\hat{Y}]^2$ exactly zero -- since $E[\hat{Y}]=\beta_0+\beta_1E[X]$. No calculus required. Just a couple elementary formulas, and a bit of completing the square.

First, notice that because $Var(A)=E[A^2]-E[A]^2$ we can write 

\begin{align*}
E[(Y-\hat{Y})^2] &= Var(Y-\hat{Y})+E[Y-\hat{Y}]^2.
\end{align*}

This peels off that last term. Turning to $Var(Y-\hat{Y})$, we know variance is translation invariant -- the variance of $A+\beta_0$ is the same as the variance of $A$. So, $Var(Y-\hat{Y})=Var(Y-\beta_1X)$. What remains is a bit of completing the square.

\begin{align*}
Var(Y-\beta_1 X)&= Cov(Y-\beta_1 X, Y-\beta_1 X)\\
&=Var(Y)-2\beta_1Cov(X, Y)+\beta_1^2Var(X)\\
&=Var(Y)-\frac{Cov(X, Y)^2}{Var(X)}+\frac{Cov(X, Y)^2-2\beta_1Var(X)Cov(X, Y)+\beta_1^2Var(X)^2}{Var(X)}\\
&=Var(Y)-\frac{Cov(X, Y)^2}{Var(X)}+\frac{(Cov(X, Y)-\beta_1Var(X))^2}{Var(X)}.
\end{align*}

This completes the proof. 

# Interpretation

Because we just used elementary algebra, we actually get a little bonus fact out of the calculation. We have shown that, when the mean square error is minimized, it is precisely $Var(Y)-\frac{(Cov(X, Y))^2}{Var(X)}$. This is a little tidier if we use $R=Cov(X, Y)/\sqrt{Var(X)Var(Y)}$. This lets us write the mean square error as 

\begin{align*}
E[(Y-\hat{Y})^2]&=Var(Y-\hat{Y})\\
&= Var(Y)(1-R^2)\\
\frac{Var(Y-\hat{Y})}{Var(Y)}&=1-R^2
\end{align*}

Furthermore, we can rewrite $\beta_1$ as $R\sqrt{Var(Y)/Var(X)}$. This shows that 

\begin{align*}
Var(\hat{Y})&=\beta_1^2Var(X)\\
&=R^2Var(Y)\\
\frac{Var(\hat{Y})}{Var(Y)}&=R^2.
\end{align*}

This is the justification for the common interpretation of $R^2$ as a fraction of total variance. We have

\begin{align*}
\underbrace{\frac{Var(\hat{Y})}{Var(Y)}}_{R^2}+\underbrace{\frac{Var(Y-\hat{Y})}{Var(Y)}}_{1-R^2}&=1.
\end{align*}

As an aside, for those with an understanding of conditional variance: this is a special case of the law of total variance, 

\begin{align*}
Var(Y)=Var(E[Y|X])+E[Var(Y|X)].
\end{align*}

# Application to sample statistics

The above method is all using the language of random variables. You may say, "Alden, that's well and good for talking about the population. I want to know about samples. You haven't helped me at all with that case!" This is a common sticking point, I think because many authors don't notice (or don't trust their readers to understand) that the formula works regardless of the measure you are using. We didn't assume much at all about $X$ and $Y$. In particular, if you have a sample $(x_1, y_1), (x_2, y_2), \dots, (x_n, y_n)$ then you can just define $X$ and $Y$ such that $P[(X, Y)=(x_i, y_i)]=\frac{1}{n}$. In that case, $E[(Y-\hat{Y})^2]$ just becomes $\frac{1}{n}\sum (y_i-\hat{y}_i)^2$, the mean square error for the sample. There is still a detail to attend to -- if we use the sample variance we have an $n-1$ in the denominator rather than an $n$ -- but you can easily verify that the difference cancels out whichever formula you use.  