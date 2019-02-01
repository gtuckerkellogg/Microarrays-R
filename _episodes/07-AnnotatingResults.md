---
title: "Annotations in R"
teaching: 5
exercises: 10
questions: 
- "How do we map our data from probe IDs to gene names?"
objectives:
- "Be aware of how different annotation data bases can be used to map different types of data." 

keypoints: 
- "BioConductor has a rich annotation infrastructure, with different data type being stored in different annotation packages."
- "The `select()` function allows us to efficiently query annotation databases."
- "Using `topTable()` in conjunction with `rownames()` allows us to retrieve all the probes which are differentially expressed between our experimental conditions." 
---

# Annotation of genomic data in R

Using our linear model, we have identified genes that are differentially expressed between
our two experimental condition. However, the results from `topTable()` only shows the
probeset ID corresponding to each differentially expressed gene. We now need to map these
IDs to gene symbols, which can then be further analyzed downstream. Fortunately, R has a
wide range of annotation packages that allows us to do this.

To do so, we will use the annotation package *hgu133plus2.db*, where *hgu133plus2* is the
array name. Intuitively, different arrays will have different annotation packages, but
they will all end with *.db*. Other than platform specific anntotation packages, there are
also sequence annotation packages (the *BSgenome* packages) as well as UCSC transcript
packages (the *UCSC.knownGenes* packages) and the organism annotation packages (the *org*)
packages. Feel free to look up the different annotation packages available on BioConductor
under the annotations tab.

## Constructing a query 

The main function we will use to annotate the data is `select()`. The general syntax for
performing the annotation is as follows:

```R
select(database, keys, keytype, columns)
```

**database** corresponds to the annotation package that we will be using to perform the
annotation. Here, **keys** are the query that we are using to perform the search (that is,
the probe IDs). We also need to provide two other information, namely **keytypes** which
tells R what type of data is the **key**. Finally, **columns** refer to the information we
want returned. However, how do we know what keytypes and columns are available? To find
out, we simply use the `keytypes()` function:

```R
keytypes(hgu133plus2.db)
```

We will see the following: 

```R
[1] "ACCNUM"       "ALIAS"        "ENSEMBL"      "ENSEMBLPROT"  "ENSEMBLTRANS" "ENTREZID"     "ENZYME"       "EVIDENCE"     "EVIDENCEALL" 
[10] "GENENAME"     "GO"           "GOALL"        "IPI"          "MAP"          "OMIM"         "ONTOLOGY"     "ONTOLOGYALL"  "PATH"        
[19] "PFAM"         "PMID"         "PROBEID"      "PROSITE"      "REFSEQ"       "SYMBOL"       "UCSCKG"       "UNIGENE"      "UNIPROT" 
```
{: .output}

> ## Try it
>
> Based on the above, what do you think is the type of key we are using to perform our query? 
> 
{: .callout}

## Performing the query

Knowing how to construct the query, we will now go onto performing the query proper. Recall that we need to know only 2 things:

1. Probeset IDs, and;
2. Columns of data to be returned from the query.

We have already discussed how to find out what columns of data we want R to return to use
using `keytypes()`. Now, we need to find a way to collect the probeset IDs of all the
genes that are differentially expressed. How do we do that?

Recall that by default, `topTable()` returns only the top ten genes. However, we can
change this behavior by specifying *n*. On the other hand, we do not know how many genes
there are that are significantly different. Fortunately, we can specify an adjusted
p-value cutoff using the *p.value* argument. By combining both *n* and *p.value*, we can
return all the genes that are significantly expressed at our desired cutoff value.

Once we retrieved all the genes, we are just one step away from getting all the probe IDs
that we need for our query. Probe IDs are stored as rownames in resulting dataframe. To
access rownames, we use the `rownames()` function. Once this is done, you can simply use
`select()` discussed above to retrieve the gene symbols of the differentially expressed
genes.

>## Try it
> 
> Using the given information, use `topTable()` to retrieve all genes that are
> differentially expressed with a adjusted p-value of less than 0.05. Thereafter, use
> `rownames()` to get a vector of probe IDs with differential gene expression between the
> two conditions. Once done, use `select()` to get the gene symbols of the differentially
> expressed genes.
{: .challenge}


