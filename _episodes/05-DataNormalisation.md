---
title: "Data processing and summarisation"
teaching: 5
exercises: 10
questions: 
- How do we perform processing of microarray data in R?
- How do we perform processing and summarisation of Affymetrix microarray data in R?
objectives:
- Background correct, normalise and summarise Affymetrix microarray data
keypoints: 
- RMA, the most widely used processing algorithm for Affymetrix data, is implemented in R
  using the `rma()` function in the `affy` package.
---

## The processes of RMA

The RMA algorithm performs steps of data processing discussed in class.

1. Background correction.
2. Normalisation.
3. Summarisation.

These steps are performed in order, and yield a processed data set size that is
considerably smallar than the data set prior to processing.


## The need for background removal of microarray data

Microarray data is intrinsically noisy with ubiquitous background noise. Thus, an
important first task that must be performed is removal of background noise. Different
algorithms deal with background differently. Most Affymetrix expression microarrays are
designed with *probe pairs*, where one probe is a *perfect match* (PM) to the target and
one is a single base *mismatch* (MM) to the target. Methods like Affymetrix's own MAS5
algorithm use the difference between PM and MM hybridisation to addreess background noise.
The RMA algorithm ignores the MM probes, and so must address background correction
directly.  The **gcrma* algorithm differs from **rma* in its background removal step.


## The need for normalisation

Sample normalisation in RMA is performed via quantile normalisation, as discussed in
class. This allows samples to be compared to each other assuming the data arise from the
same parent distribution.

### The need for summarisation

The summarisation step reduces the size of the data for each sample to the number of
measured transcripts (or genes, or exons, depending on the array). The summarisation step
in rma is performed using median polish.

## Performing RMA in R

Performing RMA  in R is relatively straightforward as it has been implemented
in the `affy` package. The function, `rma()` takes in an **AffyBatch** object, which we
have previously created when reading in our CEL files. Hence, the following one line of
code performs RMA normalisation, and returns an ExpressionSet object containing the
background corrected, normalised, and summarised expression data. 

```R
processed_data <- rma(celfiles)
```
{: .R}


> ## Try it
> If you get help on `rma()` using either `help(rma)` or `?rma`, you can see how to run
> the same process without background correction or normalisation. **Try it!**
{: .challenge}


## Manipulating an ExpressionSet object

Earlier, we have alluded to the data contained in an **ExpressionSet** object. We can
access each of these pieces of data using the following functions:

|-----------------|----------------------------|--------------------|
| Data            | Type of information        | Function           |
|-----------------|----------------------------|--------------------|
| Annotation      | Chip information           | `annotation()`     |
| PhenoData       | Phenotype data             | `pData()`          |
| Expression      | Normalised gene expression | `exprs()`          |
| Experiment data | Experimental information   | `experimentData()` |
| Feature data    | Probeset data              | `featureData()`    |

The phenotype data for each of the samples are found in the file *metadata.txt*, which is
also in the downloaded data folder. We can add the phenotype data to our ExpressionSet
object as follows:

```R
pData(processed_data) <- phenodata 
```

>## Try it
>
> Try to read in the phenotype data file using the `read.table()` function, and thereafter, add it to our ExpressionSet object. Make sure there are the same number of rows as the number of samples (that is, 20). An important aspect is to know that the phenotype data file *metadata.txt* has headers (that is, there is column name information in the file). How do we read the file in correctly? 
>
> **Hint**: Take a look at the list of all the arguments that are available in
>`read.table()`. One of them tells R if there is header information present in the file.
{: .challenge}
