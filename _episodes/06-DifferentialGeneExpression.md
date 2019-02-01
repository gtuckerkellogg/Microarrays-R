---
title: "Identifying differentially expressed genes using linear models"
teaching: 10
exercises: 10
questions: 
- "How do we identify genes that are differentially expressed in a statistically rigorous manner?"
objectives:
- "Be able to use `limma` to identify differentially expressed genes."
- "Understand the formula class of objects in R, and use it to specify the appropriate model for linear modeling." 
keypoints: 
- "The `formula` class of objects in R enables us to represent a wide range of models to identify differentially expressed genes."
---

# Identification of differentially expressed genes 

## The formula class of objects 

The formula class is the work horse of statistical modeling in R. We use `~` in the
specification of the model, where `y ~ x` means the response `y` is modelled by a linear
variable `x`. More complex models are possible using the operators `+`, `*`, `:`, and others.
Refer
to [the manual](https://stat.ethz.ch/R-manual/R-devel/library/stats/html/formula.html) for 
more information on the different operators and their meaning in a formula. Usually, in
the context of bioinformatics, we use `+` and `*`. Importantly, we can force the model to
have no intercept by indicating `+ 0` in our model formula. The intercept is the value of
our response variable when our predictor is 0, and hence might not be of interest to us
when doing differential gene expression analysis. 

## Specifying our model for differential gene expression analysis

In order to identify differentially expressed genes using linear models, we need to do two
things:

1. Generate a model matrix specifying the design, and ;
2. Generate a contrast matrix, specifying the comparisons of interest. 

The former is fits the model to the data, while the second performs statistical testing to
identify which genes are differentially expressed.

Although it sounds complicated, setting up both matrices is pretty straightforward in R, as shown below: 

```R
phenotype <- phenodata$Condition
model <- model.matrix(~ 0 + phenotype)
colnames(model) <- c("MEGM", "SCGM")
contrasts <- makeContrasts(SCGM - MEGM, levels=model)
```

In our contrast matrix, we are interested in finding out the
difference between the group grown in SCGM (the EMT phenotype) and
the control (grown in MEGM). For that reason, we used `SCGM-MEGM`, with the latter being the reference.
Naturally, you can perform more complex analysis with multiple comparisons depending on
the question of interest. You can read up more on using *limma* for more complex analysis
from the *limma* user guide (available at
https://bioconductor.org/packages/release/bioc/vignettes/limma/inst/doc/usersguide.pdf,
and in particular, pages 35-64), which demonstrates the use of *limma* for a wide range of
questions and even two-colored platforms.

> ## The importance of a good model
>
> While the process of fitting a model to the data is not difficult, the difficulty that
>is most often encountered is the choice of an appropriate model. Many times, there are
>underlying confounders that are not immediately apparent but have significant impact on
>the results. One such confounder frequently encountered in microarrays is *batch effect*,
>which arises when samples are analyzed on different days by different people, leading to
>the introduction of technical artifacts. For this reason, exploratory data analysis (EDA)
>is critical to understanding the nature of data prior to model fitting. While outside the
>scope of this practical, it is a worthwhile investment to find out some of these methods
>and also how one can correct for these technical differences in a statistically robust
>manner.
{: .callout}
 
## Model fitting
Once we have generated both model and contrast matrix, we will then proceed to fit them to the data. This is accomplished in just two lines of code in R: 

```R
fitted.model <- lmFit(processed_data, model)
fitted.contrast <- contrasts.fit(fitted.model, contrasts)
```

## Empirical Bayes correction in `limma` 

Empirical Bayes (eBayes) is a method that borrows information about the distribution
across genes to calculate a robust test statistic. In `limma`, this can be performed using
the `eBayes()` function. The function requires that we provide an object returned from
fitting a linear model (or contrast matrix) to the data. Once we have fitted the contrast
matrix to the data, performing eBayes correction is easily done in R using the following
line of code:

```R
fitted.ebayes <- eBayes(fitted.contrast)
```

## Extracting differentially expressed genes 

The function `topTable()` allows us to extract the results following our fitting of the
linear model to the data. The usage of `topTable()` is shown below:

~~~

Description:

     Extract a table of the top-ranked genes from a linear model fit.

Usage:

     topTable(fit, coef=NULL, number=10, genelist=fit$genes, adjust.method="BH",
              sort.by="B", resort.by=NULL, p.value=1, lfc=0, confint=FALSE)
     toptable(fit, coef=1, number=10, genelist=NULL, A=NULL, eb=NULL, adjust.method="BH",
              sort.by="B", resort.by=NULL, p.value=1, lfc=0, confint=FALSE, ...)
     topTableF(fit, number=10, genelist=fit$genes, adjust.method="BH",
              sort.by="F", p.value=1, lfc=0)
     topTreat(fit, coef=1, sort.by="p", resort.by=NULL, ...)

~~~
{: .output}

> ## Think about it
>
> By default, `topTable()` returns only the top ten genes ranked by their B value. How do
> you get the statistics associated with all the genes on the array? Explore the options of
> topTable to identify lists of genes that fit your criteria of interest.
{: .callout}

{% include links.md %}
