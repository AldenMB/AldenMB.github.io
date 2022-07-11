Title: Measures of Bimodality
Date: 2022-07-10
Tags: Statistics, nTARP, Clustering
Slug: bimodality
Summary: What are the most popular ways to measure the bimodality of a sample from a random variable?

Much of my research has dealt with the problem of deciding if a sample from a random variable indicates that the underlying distribution is bimodal. We have a general intuition, passed down from Francis Galton (I'm not sure where he got the idea), that random effects tend to produce unimodal distributions. This has since then been justified using the central limit theorem -- if you have lots of small random perturbations, the cumulative effect of them will be Gaussian, and hence unimodal. If your distribution is not unimodal, his argument goes, you have good reason to believe that your data are coming from two different sources, each of which has a unimodal distribution.

This is the basic principle at the heart of the nTARP clustering procedure. In this form of projection pursuit, we seek a distribution of points which appears bimodal. We set a threshold in between the two modes, then bin the data according to which side it falls upon. We hope that, because the observed distribution is bimodal, it must have originated from two distinct sources. That is what makes it a clustering method: it seeks to break apart a signal into distinct sources, based only on patterns in the data.

Up to now, nTARP has always used the within-group sum of squares statistic, denoted W. This is used under various names in many different contexts: it is the objective function which the k-means algorithm seeks to minimize in one dimension, and also the objective function of Otsu's method for image thresholding. Our results using this method have been mixed. One one hand, when a given sample is very plainly bimodal, the statistic indicates this reliably by assigning it a small W value. On the other hand we have two significant issues:

- W is significantly smaller on distributions with heavy tails. This makes it so that some distributions which appear somewhat bimodal can have a higher W than other distributions which are clearly unimodal, but which have heavy tails.
- W is not stable for small sample sizes. Because W can be computed in a way which depends continuously upon the cumulative distribution function, the Glivenko-Cantelli theorem guarantees that, as the number of samples drawn from a distribution grows, the empirical W will converge to the value of W for the underlying distribution. Unfortunately, this convergence appears to be too slow in many circumstances. For certain data sets, we find that two independent samples of hundreds of points will lead to very different estimates of the population W. Note that the meaning of "small" depends on the underlying distribution in a manner I do not yet fully understand -- for some data sets it is very stable, for others very unstable.

For this reason, we are interested in looking for other tests of bimodality. That is, given a sample of several points from a one-dimensional random variable, what techniques exist to estimate whether (or to what extent) the random variable is bimodal? Here we attempt to survey a few of the most popular techniques.

# A survey of tests

## Statistics based on labeled data

These are generally unsuitable for clustering, because they rely on data which has already been split into two proposed sources. Most of these assume each mode of the data is Gaussian. I list a few here (from Wikipedia) for completeness.

- Ashman's D
- Bimodal separation
- Bimodality amplitude
- Bimodal ratio
- Wilcock's bimodality parameter
- Wang's bimodality index
- Sambrook Smith's index

Linear discriminant analysis would also fall into this category, though it is less concerned with detecting bimodality than with fitting bimodal data.

## Kurtosis

There have been decades of arguments at this point over exactly what qualitative features are encoded by kurtosis. The only one which is generally uncontroversial is that distributions with high kurtosis have "heavy tails". Of course, this is in some sense kicking the can down the road, since people may mean different things by "heavy tailed". One of the more controversial perspectives is that kurtosis measures how bimodal a distribution is. This is usually justified by the observation that a Bernoulli random variable, which is in some sense the ideal bimodal distribution, minimizes the kurtosis among those distributions with a common variance. This interpretation is frowned upon by some, but is still widely used by researchers analyzing ordinal data with a bounded support, such as Likert scales. For a sophisticated discussion, see ["Kurtosis Measures Bimodality?" by David Hildebrand.](https://www.tandfonline.com/doi/pdf/10.1080/00031305.1971.10477241)

## Bimodality coefficient

This one is attributed to various sources, but certainly goes back at least to Karl Pearson. (skewness^2+1)/kurtosis. The rationale is the same as for kurtosis, but it always gives a number between 0 and 1. I have not found any strong theoretical justification for this statistic. One reason for its wide use is that it is available in many common statistical software packages, such as SAS. Another is its age -- it is well-established.

## Sturrock's index

A Gaussian fit is performed (any model could be used in principle, but they chose Gaussian), giving a theoretical CDF. The inverse CDF is applied to the data mapping them to quantiles between 0 and 1. Sturrock's index is the spectral power of the resulting distribution due to the frequency 2. This technique is essentially equivalent to comparing the empirical CDF to the theoretical CDF on a P-P plot. Their strategy is quite ad-hoc, probably appropriate for their domain, but would not generalize to other types of data very well. In particular, it would only make sense over a certain range of separations. The strategy of comparing the empirical CDF with a theoretical CDF is very general, and could be used to quantify how well any sample matches a given theoretical model. This would more accurately be termed a test for discrepancy from a proposed theoretical CDF, rather than a true test of multimodality. It gets to be on this list because it is substantially different from any of the other tests described here.

## Hartigan's dip test

This is based on comparing the empirical CDF to the nearest unimodal CDF. Hartigan and Hartigan show that the nearest unimodal CDF is always a mixture between the empirical CDF and a uniform distribution. The dip statistic can be computed efficiently, using some FORTRAN code which has been passed around since the 80s and translated into other languages. This only gives an index; to make it into a proper "test", Hargian and Hartigan suggest computing it on samples from a known distribution for comparison.

## Silverman's kernel density test

[Silverman](https://purdue.primo.exlibrisgroup.com/permalink/01PURDUE_PUWL/5imsd2/cdi_proquest_journals_1302943262) takes the problem of estimating a kernel density bandwidth, and turns it on its head. Usually, to get a good kernel density estimate, you need unimodal data. By choosing the smallest bandwidth which keeps the estimate unimodal, you can resolve as much detail as possible without introducing artificial wiggles. It becomes a problem if the data are bimodal, because this approach would smear out the distribution removing all detail. Silverman takes this burden and turns it into an asset: by looking for distributions which require a large bandwidth to reach unimodality, we can pick out those which are bimodal. Silverman also gives a significance test, based on bootstrapping.

As Hartigan and Hartigan note, this statistic has the disadvantage of depending on the scale of the data. You can normalize the data of course, but care must be taken to do so fairly. This would also naturally deal poorly with heavy-tailed distributions, where there are regions which are severely undersampled. Another disadvantage is that is is slow to compute. If a Gaussian kernel is chosen then the number of modes depends monotonically on the bandwidth, which allows for a binary search, but Silverman does not propose any better technique than this.

I do wonder whether we could get somewhere with a slightly more subtle version of this test, somewhat akin to the Topologist's bar codes. Might we say the correct bandwidth is not where the data becomes unimodal, but where the modality of the data does not depend strongly on the bandwidth? That is, there should be a wide range of bandwidths over which the density estimate has the same modality. That range might tell us more than just a single number can.

## information-theoretic approaches

[Some people](https://link.springer.com/content/pdf/10.3758%2Fs13428-012-0225-x.pdf) seem to be applying an information-theoretic approach as well, to see whether a two-component mixture model does a significantly better job of expressing the information in a sample. This is a purely parametric approach, in that the underlying distribution is assumed.

# Conclusions

Three of the above methods see widespread use across disciplines: the bimodality coefficient, the Hartigan dip test, and Silverman's kernel density test.Many papers cite these three together, in a scattershot approach to statistical analysis. For some reason, the statistic W only sees much use in the image processing community, presumably due to the influence of Otsu's method for image segmentation, and even there is goes by another name. I have not seen much written with the goal of improving on these statistics. I think most authors find it more valuable to have statistics which are directly comparable to other researchers' numbers, rather than seeking marginal improvements to the computation methods.

Of course, if a parametric form for the data are known, far more options are available -- one could use gradient descent to estimate parameters, for example. In the case where more is known about the parametric form of the data, we would expect a custom method to work better in principle because it does not have to learn what we already know. We are looking for a truly general statistic, something which does not depend on the form of the distribution. This rules out most published approaches other than the four identified above.