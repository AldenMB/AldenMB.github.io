Title: We should all use OpenIntro Statistics
Date: May 14 2025
Category: Blog
Tags: OER, Statistics, Teaching
Summary: My argument for switching textbooks

# Background

In Washington State, we teach a unified statistics curriculum across all the community colleges. The course is called Math&146, and our rule for what goes into it is the American Statistical Association's [Guidelines for Assessment and Instruction in Statistics Education](https://www.amstat.org/education/guidelines-for-assessment-and-instruction-in-statistics-education-(gaise)-reports) (GAISE) College Report. Currently, most instructors at Olympic College use the OER textbook [Introductory Statistics](https://openstax.org/details/books/introductory-statistics-2e) published by OpenStax. I believe we should transition away from the OpenStax textbook, and instead adopt a different OER textbook, published by a different group with a similar name: [OpenIntro](https://openintro.org/book/os/).

Introductory Statistics, published by OpenStax, has two primary authors and eight contributing authors. The primary authors were professors at De Anza College, a community college in Cupertino California. They are both now retired. One of them has a master's degree in statistics and a PhD in education, and the other has a master's degree in applied mathematics. Out of the eight other contributing authors, one has a master's degree in statistics. The rest have degrees in mathematics or related disciplines.
<!---
Barbara Illowsky: MS Statistics, Wharton at University of Pennsylvania. PhD Education, Capella University.
Susan Dean: MS Applied Mathematics, Santa Clara University.
http://nebula2.deanza.edu/~kathyplum/story.html

Daniel Birmajer: PhD mathematics
Bryan Blount: MA education
Sheri Boyd: PhD mathematics
Matthew Einsohn: MA Environmental Studies
James Helmreich: BA Bowdoin College (Mathematics, Economics) MA University of Maryland (Mathematical Logic) PhD University of Maryland (Mathematical Logic) MS SUNY at Albany (Statistics)
Lynette Kenyon: math professor
Sheldon Lee: PhD mathematics
Jeffrey Taub: MS logistics
-->

OpenIntro Statistics was created by three PhD students at UCLA, each of whom completed their PhD in statistics. Two of them later did postdocs in statistics, one of them is now a statistics professor at Duke University. It is currently in its fourth edition, with a fifth on the way. This is a mature textbook, the first edition being published in 2011. Also, unlike the OpenStax book, OpenIntro has the approval of the American Institute of Mathematics [Open Textbook Initiative](https://textbooks.aimath.org/).
<!---
Mine Çetinkaya-Rundel: MS and PhD in Stats, UCLA. Prof. at Duke University in Stats.
David Diez: PhD in Stats, UCLA. Postdoc at Harvard. Works at YouTube.
Chris Barr: PhD in Stats, UCLA. Postdoc at Johns Hopkins.
-->

I have a few different reasons I think we should switch, and it amounts to more than just a matter of personal preference. On a couple key topics, the exposition given in OpenStax is arguably incorrect from a statistical point of view. More broadly, OpenIntro has better alignment with the standards laid out in GAISE, in ways I will explain below. I think this difference can mostly be attributed to the different backgrounds of the authors. The OpenStax book was written by mostly mathematicians, while the OpenIntro book was written entirely by statisticians. Particularly since we have more mathematical expertise than statistical expertise among the faculty here at Olympic College, it would be better for us to use the book with a stronger statistical bent.

# Necessary improvements

We could argue about preferences indefinitely and never reach a conclusion. There are lots of stylistic, pacing, and presentation choices which matter a lot for textbooks. There is no one best way to write a book. That's why I want to start with a couple of things which are not just preferences. There are two major areas where OpenIntro Statistics is right, and OpenStax is wrong. Even if you do not switch to OpenIntro, you should be aware of these problems so as to limit the spread of false statistics information.

## Retrospective One-Tailed Tests

There is a fundamental issue with how OpenStax structures the hypothesis testing chapter. Consider the [Try It example 9.19 from the OpenStax text](https://openstax.org/books/introductory-statistics-2e/pages/9-5-additional-information-and-full-hypothesis-test-examples):

> A car soap gets rid of 30% of stains on the car. After adding a new compound to the soap, the soap is used on a car and found to wash 20 stains out of the 50 stains on the car. With the level of significance being 10%, find out if adding the new compound to soap is beneficial.

We know that 40% of stains in the sample were removed, we want to know if that difference is statistically significant. I will set aside concerns about whether the stains are really independent events. On problems like this, the OpenStax text instructs you to do a one-tailed test. Using the null hypothesis value of $p=30\%$ we would expect to see samples where at least 20 stains are removed about 8.5% of the time. That's less than our rejection threshold of $\alpha=10\%$, so we reject the null hypothesis and conclude the new soap is beneficial.

That's the solution OpenStax expects. That solution is also wrong.

To understand the error, let's see what would have happened if instead of 20 stains, the soap managed to get rid of 10 stains. If this had happened, we would have asked: with a significance level of 10%, is the new compound detrimental? Again, using $p=30\%$, we would expect to see samples where at most 10 stains are removed about 7.9% of the time.  That's less than the threshold of $\alpha=10\%$, so we reject the null hypothesis and conclude the new soap is detrimental.

I hope the error is becoming clearer. The problem has us inspecting the data before we decide what test to use. Following the OpenStax procedure, we end up rejecting the null hypothesis $8.5\%+7.9\%=16.4\%$ of the time, assuming the null hypothesis is true. That should not be possible with the significance level chosen. $\alpha$ is meant to be the probability of rejecting the null hypothesis assuming the null hypothesis is correct. We should have done a 2-tailed test, and we should not have rejected the null hypothesis at all.

Some of the problems in OpenStax are more carefully worded, and make it clear that the researcher picked the test to use before inspecting the data. Not all of the questions are so careful. Any time we inspect the data before deciding on which direction of test to use, we must do a 2-tailed test. Otherwise, we end up doubling our $\alpha$ value by mistake.

For this reason, the OpenIntro book defers one-tailed tests to a special topic. Most of the book avoids one-tailed tests.

It is true that there are cases where a one-tailed test is appropriate. For example, when you try a new treatment against the null hypothesis of "this does no better than the best currently established treatment", it makes perfect sense to do a one-tailed test. You will get a little more statistical power that way. However, for almost every case our students are likely to encounter, it is safer to just do a 2-tailed test every time. Some disciplines have banned one-tailed tests entirely, because they are so often misapplied. When I teach Math&146, I do not cover one-tailed tests. The consequence is that the rules for students to learn are simpler, and the students' results are more accurate. That's a win!

For a nuanced discussion of this issue, see [Misprescription and misuse of one-tailed tests (2009)](https://biology.sdsu.edu/pub/stuart/2009MisprescriptionOneTailed.pdf), [When should we use one-tailed hypothesis testing? (2010)](https://besjournals.onlinelibrary.wiley.com/doi/full/10.1111/j.2041-210X.2010.00014.x) and [One-tailed tests: Let's do this (responsibly) (2024)](https://pubmed.ncbi.nlm.nih.gov/37917504/).

## T test or Z test

There are several places in statistics where a z-test is always the right test to do. For example, when we are computing a population proportion confidence interval, we are safe to use a z-score whenever $np\geq 10$ and $n(1-p)\geq 10$ because the central limit theorem kicks in very fast for the binomial distribution. Since we use the binomial distribution formula to compute the standard deviation, we don't need the small-sample correction that Student's T distribution provides. One of the improvements of the OpenIntro text is that it starts the inferential statistics section looking only at sample proportions. With only the normal distribution to compare against, students have more energy to pay attention to the hypothesis testing framework -- terms like standard error, point estimate, confidence interval, and p value. Later on, once students are used to the framework, we introduce the t-test.

When I was first taught statistics, I was taught to use a t-distribution for sample sizes less than 30. For larger samples, we were told to use the z-distribution since the t-distribution converges asymptotically to a standard normal distribution when the sample size is larger. This is the strategy taught by OpenStax. When I was a kid, this made sense -- the textbook only had t-tables for sample sizes up to 30. These days, most people are not looking up values in a table, they are using computer software to find the t-scores. When you have a computer on hand, it does not make sense to deal with a special case for large samples. It is much simpler to just use the t-distribution for anything where you are using the sample variance. The CDF is a hypergeometric function, which is one of the fastest types of special function to compute by machine. The OpenIntro book teaches students to use a t-score regardless of sample size. This method is easier to remember, less prone to error, and gives slightly more accurate results. That's a win!

As an aside, I will note one other case where OpenStax advises students to use a z-test. That's in the case where the population standard deviation is known, but the population mean is unknown. From a mathematical standpoint, this approach is sound. From a statistical standpoint, it is unfounded. There are almost no cases in practice where you would know the standard deviation of a population but you would not know the mean. This case has some merit as a teaching tool, a stepping stone, emphasizing the reason why the t-test is necessary. I believe this would be a worthwhile special topic in a more advanced probability class, particularly one where students will derive the t-test for themselves. It does not belong in an introductory stats class. OpenIntro does without it.

![A comparison of decision trees used by each textbook for hypothesis testing]({attach}decision_tree.svg)

# Technology

Modern statistics is not done without computers. GAISE exhorts us to
> -5. Use technology to explore concepts and analyze data.

Two of the Goals for Students in Introductory Statistics Courses are

> -3. Students should be able to produce graphical displays and numerical summaries and interpret what graphs do and do not reveal.

> -8. Students should be able to interpret and draw conclusions from standard output from
statistical software packages.

OpenIntro and OpenStax are built around different computer tools. My philosophy for choosing a tool is that it should have a low floor and a high ceiling.

A low floor means that students should be able to get useful results after just one 50-minute lesson using the tool. This precludes a general-purpose programming language like Python. In my experience, it takes at least two lessons in Python before students are comfortable enough with the syntax to be able to import the required libraries and use the relevant functions.

A high ceiling means that students should be able to continue to use the tool throughout their education and career. The tools that we teach should be powerful enough that students will not have to abandon them when their next course, or next job, comes along.

## The TI-84

Every problem in the OpenStax book can be solved using the TI-84. There are even helpful guides sprinkled throughout, explaining exactly which buttons to press. This tool definitely meets our low floor criterion. Many, possibly most, of our students have used a similar calculator in high school. Students can often get useful results without any new instruction on our part. Unfortunately, the TI-84 does not have a very high ceiling. It is hard to load data onto it. It is not practical to export the graphics it creates. Even as we teach Math&146 now, we often supplement with web applets that do much better than the TI-84 at specific tasks. There was a period in the 80s and 90s when it would have been an acceptable tool for a professional context, but that time has long since passed.

Why does OpenStax use the TI-84? I believe this can be traced back to Texas Instruments' educational outreach division. In order to maintain their market dominance in the high school education space, TI has produced excellent educational resources to help high school teachers prepare their students for standardized tests, notably the AP Stats exam. The authors of the OpenStax book made an effort to incorporate the TI-84 emulator into their curriculum, which Texas Instruments made free for anyone to use, as a way to promote the use of the calculator. Using those educational resources was an economical way to get technology into the curriculum, helping OpenStax get established quickly.

I am a fan of Texas Instruments. Their calculators are excellent. They are the makers of my personal favorite calculator, the [TI-30Xa](https://aldenbradford.com/calculator_emulator/). I still think the TI-84 is the wrong tool to teach college statistics.

## Spreadsheets

By far the most common statistical tool used in industry is the humble spreadsheet. No matter what our students end up studying, no matter where they work, they will have access to spreadsheet software. Lots of tasks in intro statistics can be handled with a spreadsheet alone: means, medians, quartiles, standard deviations, even some chi squared tests can be done without leaving the spreadsheet. Students don't need much instruction to get underway with a spreadsheet because many of them have used spreadsheets in other contexts. The graphical interface means spreadsheets certainly meet our low floor criterion. Some people make whole careers out of spreadsheet wizardry, so it also has a high ceiling.

I teach my students to use spreadsheets for data acquisition, data cleaning, and basic summary statistics. I teach them using LibreOffice Calc, which is a free spreadsheet program available on every operating system. The skills are transferable to other spreadsheet programs.

I don't leave it there. Once students know how to format their data correctly, I proceed quickly to R.

## R

R is a completely free, open-source statistics and data visualization package which has been around since 1993, based closely on its predecessor S which was created at Bell Laboratories in 1976. Unlike Python, it is not meant as a general-purpose programming language. Rather, it is designed specifically to simplify routine statistics tasks. All interaction in R can be done through a command line, and the commands are simple enough that my students rarely have trouble formatting them correctly. Here is an example of an R session a student might use to answer a homework problem.

```R
> x <- c(1, 1, 2, 2, 3, 5, 7, 10)
> summary(x)
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
  1.000   1.750   2.500   3.875   5.500  10.000 
> sd(x)
[1] 3.226564
> boxplot(x)
```
![the boxplot produced by R]({attach}boxplot.png)

Unlike many simple applets, R will correctly terminate the whiskers at $1.5\times IQR$ away from the quartiles. This is how it behaves without any additional configuration. R satisfies the low floor criterion: my students are eager to start using it for their homework the day they learn it.

R also has a very high ceiling. It is a tool used by professional data scientists for exploratory analysis, simulation, and creating publication-quality graphics. You may have seen the popular website FiveThirtyEight. They used R to generate most of their award-winning data visualizations. Just as some people have made a career on being expert in spreadsheets, some people make their living using R.

The OpenIntro textbook comes with a set of labs which get students solving problems relevant to the textbook chapters, using R. These are meant for a class with a weekly computer lab session supervised by a teaching assistant. I have adapted several of these into homework projects, with associated in-class lessons. The projects are not very complicated, typically they will take less than an hour to complete. They are mostly graded automatically through Canvas. Their purpose is to get students using the statistical tools on some real data. 

GAISE cautions us that

> SAS certification, non-introductory R programming, and other more extensive programming topics belong in subsequent courses.

I do not teach my students about string manipulation, looping, conditionals, function writing, or any object-oriented aspects of R. The most basic parts, essentially using R as a calculator, are more than sufficient for what we need.

# Using Real Data

GAISE is unequivocal: 
> Recommendation 3: Integrate real data with a context and a purpose.
> Using real data in context is crucial in teaching and learning statistics, both to give students experience with analyzing genuine data and to illustrate the usefulness and fascination of our discipline.

On this score, OpenIntro does much, much better than OpenStax. OpenIntro is littered with real data sets, complete with a context and a citation. The included lecture slides also include additional data sets not covered in the main book, so students get more exposure to the process of exploring data. Every example in the text comes with a cleaned-up copy of the data, ready to download from teh website or as an R package. This makes it easy to create your own graphics, follow-up exercises, and tests based on the same data used in the book.

Contrast this with OpenStax. Its data appendix contains only 2 data sets, and their provenance is dubious. Most of the exercises in the book use made-up values. This can be a good thing! A well-chosen example, used to illustrate an important point, doesn't always have to be true. However, we deprive our students when the only examples we give are made-up. It is ubiquitous to use made-up examples in a math class. It should not be so common in a statistics class.

When I taught from OpenStax, I tried to compensate for this by using data collected with in-class experiments. This worked okay. Now that I am using spreadsheets and R, there are many, many more choices of data available. I recommend [Vincent Arel-Bundock's list](https://vincentarelbundock.github.io/Rdatasets/articles/data.html) of just about every data set anyone has published for free as part of an R package. Every one of these comes with a description of how the data was collected and suggestions of how it can be analyzed. By picking from these data sets, it is easy to find data with the types of variables I am looking for. Also, in some cases, the students will already have the data on their machine ready to load in R.

It can be tempting to make up examples for short problems, like making confidence intervals for a population proportion. Since we won't work with them very long, it doesn't make sense to spend a lot of time explaining the context. However, these are also some of the easiest examples to find suitable data for. Our library has a subscription to Harper's magazine, which every month publishes [Harper's Index](https://harpers.org/harpers-index/). This is a list of brief, interesting statistics from the news. I regularly peruse Harper's Index for examples to use in class or in quizzes. I find that students are more engaged when we use a number from the news, even if the mathematics is the same.

# Topic Coverage

Most of the topic coverage overlaps between the two books. Here I will point out a few notable differences, as we transition from OpenStax to OpenIntro.

## Stem-and-leaf plots
I love stem-and-leaf plots. They hold a place dear to my heart. I also adore the slide rule and adding machine -- I own more slide rules and adding machines than could ever be practical. Alas, OpenIntro does not cover the stem-and-leaf plot, and I (reluctantly) must agree that it probably does not belong in an intro stats course, at least not any more than a slide rule belongs in an introductory algebra course. It is a neat device, but it is mostly practical when a pencil and paper are the best tools at hand for data visualization. Today, when a histogram is just a few clicks away, there is no need to bother a new student with the rules of the stem and leaf plot. The role of the stem-and-leaf plot is mostly supplanted by the dot plot and the histogram. GAISE cautions us against

> Constructing plots by hand. Data displays are now made by computers. Students need to
know how to read and interpret them. Instead of spending lots of time creating
histograms by hand, use some of that time instead to develop a deeper understanding and
ask more challenging questions about what the plots tell us about the data.

I do still have my students create boxplots by hand, with some technological assistance. I feel this is the best way to ensure they can really interpret the whiskers and the outliers. I don't make them compute medians and quartiles by hand, since that process is prone to error. When students mess up computing the median, it is rarely because of a conceptual misunderstanding.

## Probability Theory

Here is where my review of OpenIntro is more measured. As OpenIntro was written by statisticians, it carries a statistician's understanding of probability theory. The section on random variables leaves much to be desired. For example, the OpenIntro book comes with a mostly-excellent set of slides suitable for lectures. The slides on random variables include the bold claim that

> Random variables do not work like normal algebraic variables: $$X+X\neq 2X$$

This is nonsense. The whole point of random variables is that they do work just like algebraic variables. What the authors mean here is that, given two i.i.d. variables $X_1$ and $X_2$, the random variable $X_1+X_2$ will in general not have the same distribution as $2X_1$. In particular, while $Var(X_1+X_2)=Var(X_1)+Var(X_2)=2Var(X_1)$, $Var(2X_1)=4Var(X_1)$. This is a useful point to make, but the way the authors make it is confused and a little clumsy.

If I were choosing a textbook for an introductory probability course, I would probably go with one of the texts by Sheldon Ross. Alas, I do not think any OER textbook exists which is up to my standards for a probability book. Luckily, in our case, we are not choosing a book for a probability course. GAISE tells us

> The original GAISE report recommended less emphasis on probability in the introductory course and we continue to endorse that recommendation.

I think the OpenIntro text is well-suited to our course, despite its relatively poor coverage of random variables. I just skip those sections entirely. For comparison, the OpenStax book makes no explicit reference to the algebra of random variables. Also, while the above error does appear in the slides, I have not found any similar error in the text itself. The textbook section on random variables is probably not presented the way I would do it as a probability theorist, but the information presented in the book is correct. There is no danger posed to the curious student who reads outside the required sections.

# Aesthetics

The OpenStax book borrows from many other OER textbooks, dating back to at least 2007. There is a mix of many different graphical styles, citation styles, and problem formats. Personally, I feel the book has a disjointed tone. The sections don't flow together, making the book hard to read. I think this is part of why so few of our students read the book. Also, the paperback version is laid out based on the online version of the text, with unnecessarily big margins leading to a bulky book.

The OpenIntro book has a much more consistent tone and flow, having been created and maintained by relatively few authors. They make available the configuration files needed to create your own figures in the same style as the book, if you wish. The end-of-chapter problems are of about the same length across chapters. The problems also reference previous problems and examples from the text, which gives a sense of planning and cohesion to the narrative.

Aesthetic considerations naturally take a back seat compared to concerns about accuracy, accessibility, and content coverage. Aesthetics still matters, because it affects how much and how well students will use the textbook. From my experience so far, I believe that my students are using the OpenIntro textbook more, compared to how my students used the OpenStax textbook in the past.

# Recommendation

Over the course of, say, the next two years, we should transition away from OpenStax for statistics. I do like many other OpenStax books, but for Statistics, OpenIntro is better. By transitioning we will be teaching our students more accurate information, we will  have better alignment with GAISE standards, and our student will have a better reading experience.

I don't expect that we will change textbooks over night. It will take time for professors to learn the new order of the topics, prepare new lesson plans, and prepare new assessment materials. We should be compensated for that time. The number one request I hear from my students in this course is to have more homework. Many of the online problems we use in the OpenStax course could be adapted to the newer textbook. There are many problems in the printed OpenIntro textbook that don't have online randomized versions yet. This is an area where I would welcome collaboration with colleagues more experienced in online homework creation.