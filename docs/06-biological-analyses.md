---
output: html_document
---


```
## Loading required package: SummarizedExperiment
```

```
## Loading required package: GenomicRanges
```

```
## Loading required package: stats4
```

```
## Loading required package: BiocGenerics
```

```
## Loading required package: parallel
```

```
## 
## Attaching package: 'BiocGenerics'
```

```
## The following objects are masked from 'package:parallel':
## 
##     clusterApply, clusterApplyLB, clusterCall, clusterEvalQ,
##     clusterExport, clusterMap, parApply, parCapply, parLapply,
##     parLapplyLB, parRapply, parSapply, parSapplyLB
```

```
## The following objects are masked from 'package:stats':
## 
##     IQR, mad, sd, var, xtabs
```

```
## The following objects are masked from 'package:base':
## 
##     anyDuplicated, append, as.data.frame, basename, cbind,
##     colMeans, colnames, colSums, dirname, do.call, duplicated,
##     eval, evalq, Filter, Find, get, grep, grepl, intersect,
##     is.unsorted, lapply, lengths, Map, mapply, match, mget, order,
##     paste, pmax, pmax.int, pmin, pmin.int, Position, rank, rbind,
##     Reduce, rowMeans, rownames, rowSums, sapply, setdiff, sort,
##     table, tapply, union, unique, unsplit, which, which.max,
##     which.min
```

```
## Loading required package: S4Vectors
```

```
## 
## Attaching package: 'S4Vectors'
```

```
## The following object is masked from 'package:base':
## 
##     expand.grid
```

```
## Loading required package: IRanges
```

```
## Loading required package: GenomeInfoDb
```

```
## Loading required package: Biobase
```

```
## Welcome to Bioconductor
## 
##     Vignettes contain introductory material; view with
##     'browseVignettes()'. To cite Bioconductor, see
##     'citation("Biobase")', and for packages 'citation("pkgname")'.
```

```
## Loading required package: DelayedArray
```

```
## Loading required package: matrixStats
```

```
## 
## Attaching package: 'matrixStats'
```

```
## The following objects are masked from 'package:Biobase':
## 
##     anyMissing, rowMedians
```

```
## Loading required package: BiocParallel
```

```
## 
## Attaching package: 'DelayedArray'
```

```
## The following objects are masked from 'package:matrixStats':
## 
##     colMaxs, colMins, colRanges, rowMaxs, rowMins, rowRanges
```

```
## The following objects are masked from 'package:base':
## 
##     aperm, apply
```

```
## Loading required package: ggplot2
```

```
## 
## Attaching package: 'scater'
```

```
## The following object is masked from 'package:S4Vectors':
## 
##     rename
```

```
## The following object is masked from 'package:stats':
## 
##     filter
```


# Biological Analysis

## Clustering 

Once we have normalized the data and removed confounders we can 
carry out analyses that are relevant to the biological questions 
at hand. The exact nature of the analysis depends on the dataset.
Nevertheless, there are a few aspects that are useful in a wide 
range of contexts and we will be discussing some of them in the 
next few chapters. We will start with the clustering of 
scRNA-seq data.

### Introduction

One of the most promising applications of scRNA-seq is _de novo_ 
discovery and annotation of cell-types based on transcription
profiles. Computationally, this is a hard problem as it amounts to
__unsupervised clustering__. That is, we need to identify groups of
cells based on the similarities of the transcriptomes without any
prior knowledge of the labels. Moreover, in most situations we
do not even know the number of clusters _a priori_. The problem
is made even more challenging due to the high level of noise 
(both technical and biological) and the large number of dimensions
(i.e. genes). 

### Dimensionality reduction

When working with large datasets, it can often be beneficial to
apply some sort of dimensionality reduction method. By projecting
the data onto a lower-dimensional sub-space, one is often able to
significantly reduce the amount of noise. An additional benefit is
that it is typically much easier to visualize the data in a 2 or
3-dimensional subspace. We have already discussed PCA 
and t-SNE. 

### Clustering methods

__Unsupervised clustering__ is useful in many different applications and
it has been widely studied in machine learning. Some of the most
popular approaches are __hierarchical clustering__, 
__k-means clustering__ and __graph-based clustering__.

#### Hierarchical clustering

In [hierarchical clustering](https://en.wikipedia.org/wiki/Hierarchical_clustering),
one can use either a bottom-up or a top-down approach. In the former
case, each cell is initially assigned to its own cluster and
pairs of clusters are subsequently merged to create a hieararchy:




```r
knitr::include_graphics("figures/hierarchical_clustering2.png")
```

<div class="figure" style="text-align: center">
<img src="figures/hierarchical_clustering2.png" alt="The hierarchical clustering dendrogram" width="50%" />
<p class="caption">(\#fig:clust-hierarch-dendr)The hierarchical clustering dendrogram</p>
</div>

With a top-down strategy, one instead starts with all
observations in one cluster and then recursively split each
cluster to form a hierarchy. One of the advantages of 
this strategy is that the method is deterministic.

#### k-means

In [_k_-means clustering](https://en.wikipedia.org/wiki/K-means_clustering),
the goal is to partition _N_ cells into _k_ different clusters. 
In an iterative manner, cluster centers are assigned and
each cell is assigned to its nearest cluster:


```r
knitr::include_graphics("figures/k-means.png")
```

<div class="figure" style="text-align: center">
<img src="figures/k-means.png" alt="Schematic representation of the k-means clustering" width="100%" />
<p class="caption">(\#fig:clust-k-means)Schematic representation of the k-means clustering</p>
</div>

Most methods for scRNA-seq analysis includes a _k_-means 
step at some point.

#### Graph-based methods

Over the last two decades there has been a lot of interest in
analyzing networks in various domains. One goal is to
identify groups or modules of nodes in a network.


```r
knitr::include_graphics("figures/graph_network.jpg")
```

<div class="figure" style="text-align: center">
<img src="figures/graph_network.jpg" alt="Schematic representation of the graph network" width="100%" />
<p class="caption">(\#fig:clust-graph)Schematic representation of the graph network</p>
</div>

Some of these methods can be applied to scRNA-seq data
by building a graph where each node represents a cell.
Note that constructing the graph and assigning weights
to the edges is not trivial. One advantage of 
graph-based methods is that some of them are very 
efficient and can be applied to networks containing
millions of nodes.

### Challenges in clustering

* What is the number of clusters _k_?
* What is a cell type?
* __Scalability__: in the last few years the number of cells in scRNA-seq experiments has grown by several orders of magnitude from ~$10^2$ to ~$10^6$
* Tools are not user-friendly

### Software packages for scRNA-seq data

#### [clusterExperiment](https://www.bioconductor.org/packages/release/bioc/html/clusterExperiment.html) 

* R/Bioconductor package uses `SingleCellExperiment` object
* Functionality for running and comparing many different clusterings of single-cell sequencing data or other large mRNA expression data sets
* Based on the RSEC (Resampling-based Sequential Ensemble Clustering) algorithm for finding a single robust clustering based on the many clusterings that the user might create by perturbing various parameters of a clustering algorithm 

#### [SC3](http://bioconductor.org/packages/SC3/)


```r
knitr::include_graphics("figures/sc3.png")
```

<div class="figure" style="text-align: center">
<img src="figures/sc3.png" alt="SC3 pipeline" width="100%" />
<p class="caption">(\#fig:clust-sc3)SC3 pipeline</p>
</div>

* R/Bioconductor package based on PCA and spectral dimensionality reductions [@Kiselev2016-bq]
* Utilises _k_-means
* Additionally performs the consensus clustering

#### tSNE + k-means

* Based on __tSNE__ maps
* Utilises _k_-means

#### [SINCERA](https://research.cchmc.org/pbge/sincera.html)

* SINCERA [@Guo2015-ok] is based on hierarchical clustering
* Data is converted to _z_-scores before clustering
* Identify _k_ by finding the first singleton cluster in the hierarchy

#### [pcaReduce](https://github.com/JustinaZ/pcaReduce)

pcaReduce [@Zurauskiene2016-kg] combines PCA, _k_-means and “iterative” hierarchical clustering. Starting from a large number of clusters pcaReduce iteratively merges similar clusters; after each merging event it removes the principle component explaning the least variance in the data.

#### [SNN-Cliq](http://bioinfo.uncc.edu/SNNCliq/)

`SNN-Cliq` [@Xu2015-vf] is a graph-based method. First the method identifies the k-nearest-neighbours of each cell according to the _distance_ measure. This is used to calculate the number of Shared Nearest Neighbours (SNN) between each pair of cells. A graph is built by placing an edge between two cells If they have at least one SNN. Clusters are defined as groups of cells with many edges between them using a "clique" method. SNN-Cliq requires several parameters to be defined manually.

#### Seurat clustering

[`Seurat`](https://github.com/satijalab/seurat) clustering is based on a _community detection_ approach similar to `SNN-Cliq` and to one previously proposed for analyzing CyTOF data [@Levine2015-fk]. Since `Seurat` has become more like an all-in-one tool for scRNA-seq data analysis we dedicate a separate chapter to discuss it in more details (chapter \@ref(seurat-chapter)).

### Comparing clustering

To compare two sets of clustering labels we can use [adjusted Rand index](https://en.wikipedia.org/wiki/Rand_index). The index is a measure of the similarity between two data clusterings. Values of the adjusted Rand index lie in $[0;1]$ interval, where $1$ means that two clusterings are identical and $0$ means the level of similarity expected by chance.




## Clustering example


```r
library(SC3)
```

To illustrate clustering of scRNA-seq data, we consider 
the `Deng` dataset of cells from developing mouse embryo 
[@Deng2014-mx]. We have preprocessed the dataset and 
created a `SingleCellExperiment` object in advance. 
We have also annotated the cells with the cell types 
identified in the original publication (it is the 
`cell_type2` column in the `colData` slot).

### Deng dataset

Let's load the data and look at it:

```r
deng <- readRDS("data/deng/deng-reads.rds")
deng
```

```
## class: SingleCellExperiment 
## dim: 22431 268 
## metadata(0):
## assays(2): counts logcounts
## rownames(22431): Hvcn1 Gbp7 ... Sox5 Alg11
## rowData names(10): feature_symbol is_feature_control ...
##   total_counts log10_total_counts
## colnames(268): 16cell 16cell.1 ... zy.2 zy.3
## colData names(30): cell_type2 cell_type1 ... pct_counts_ERCC
##   is_cell_control
## reducedDimNames(0):
## spikeNames(1): ERCC
```

Let's look at the cell type annotation:

```r
table(colData(deng)$cell_type2)
```

```
## 
##     16cell      4cell      8cell early2cell earlyblast  late2cell 
##         50         14         37          8         43         10 
##  lateblast   mid2cell   midblast         zy 
##         30         12         60          4
```

A simple PCA analysis already separates some strong 
cell types and provides some insights in the data structure:


```r
plotPCA(deng, colour_by = "cell_type2")
```

<img src="06-biological-analyses_files/figure-html/unnamed-chunk-5-1.png" width="90%" style="display: block; margin: auto;" />

As you can see, the early cell types separate quite well, 
but the three blastocyst timepoints are more difficult to distinguish.

### SC3

Let's run `SC3` clustering on the Deng data. The advantage 
of the `SC3` is that it can directly ingest a 
`SingleCellExperiment` object.

Now let's image we do not know the number of clusters _k_ 
(cell types). `SC3` can estimate a number of clusters for you:


```r
deng <- sc3_estimate_k(deng)
```

```
## Estimating k...
```

```r
metadata(deng)$sc3$k_estimation
```

```
## [1] 6
```

Interestingly, the number of cell types predicted by `SC3`
is smaller than in the original data annotation. However, 
early, mid and late stages of different cell types 
together, we will have exactly 6 cell types. We store 
the merged cell types in `cell_type1` column of the
`colData` slot:


```r
plotPCA(deng, colour_by = "cell_type1")
```

<img src="06-biological-analyses_files/figure-html/unnamed-chunk-7-1.png" width="90%" style="display: block; margin: auto;" />

Now we are ready to run `SC3` (we also ask it to calculate
biological properties of the clusters)


```r
deng <- sc3(deng, ks = 10, biology = TRUE)
```

`SC3` result consists of several different outputs
(please look in [@Kiselev2016-bq] and 
[SC3 vignette](http://bioconductor.org/packages/release/bioc/vignettes/SC3/inst/doc/my-vignette.html)
for more details). Here we show some of them:

First up we plot a "consensus matrix". The consensus matrix is a 
`NxN` matrix, where `N` is the number of cells. It represents similarity 
between the cells based on the averaging of clustering results from all 
combinations of clustering parameters. 

Similarity 0 (blue) means that the two cells are always assigned to 
different clusters. In contrast, similarity 1 (red) means that the two
cells are always assigned to the same cluster. The consensus matrix is 
clustered by hierarchical clustering and has a diagonal-block structure. 
Intuitively, the perfect clustering is achieved when all diagonal blocks
are completely red and all off-diagonal elements are completely blue.

We can plot the consensus matrix as a heatmap using the `sc3_plot_consensus()` 
in the `SC3` package. 


```r
sc3_plot_consensus(deng, k = 10, show_pdata = "cell_type2")
```

<img src="06-biological-analyses_files/figure-html/unnamed-chunk-9-1.png" width="90%" style="display: block; margin: auto;" />

Next, we can create a heatmap of the gene expression matrix using 
the `sc3_plot_expression()` function in `SC3`. 

The expression heatmap represents the original input expression matrix 
(cells in columns and genes in rows) after applying a gene filter. Genes 
are clustered by kmeans with `k = 100` (dendrogram on the left) and the
heatmap represents the expression levels of the gene cluster centers 
after log2-scaling. We also ask for `k = 10` clusters of cells. 


```r
sc3_plot_expression(deng, k = 10, 
                    show_pdata = "cell_type2")
```

<img src="06-biological-analyses_files/figure-html/unnamed-chunk-10-1.png" width="90%" style="display: block; margin: auto;" />


We can also create PCA plot with clusters identified from `SC3`:


```r
plotPCA(deng, colour_by = "sc3_10_clusters")
```

<img src="06-biological-analyses_files/figure-html/unnamed-chunk-11-1.png" width="90%" style="display: block; margin: auto;" />

We can compare the results of `SC3` clustering with the 
original publication cell type labels using the 
[Adjusted Rand Index](https://en.wikipedia.org/wiki/Rand_index#Adjusted_Rand_index). 

The Rand index (named after William M. Rand) is a measure of the similarity
between two data clusterings. The adjusted Rand index is the Rand index 
that is adjusted for the chance grouping of elements. Such a correction for chance 
establishes a baseline by using the expected similarity of all pair-wise comparisons
between clusterings specified by a random model. The adjusted Rand index is thus 
ensured to have a value close to 0.0 for random labeling independently of the 
number of clusters and samples and exactly 1.0 when the clusterings are identical. 

Here, we use the function `adjustedRandIndex()`, which is part of the `mclust` 
package: 


```r
mclust::adjustedRandIndex(colData(deng)$cell_type2,
                          colData(deng)$sc3_10_clusters)
```

```
## [1] 0.6638246
```

__Note__ `SC3` can also be run in an interactive `Shiny` 
session:


```r
sc3_interactive(deng)
```

This command will open `SC3` in a web browser.

Before we leave this section, the package vignette points out that 
because of direct calculations of distances, the `SC3` functions will
become very slow when the number of cells is $>5000$.


### tSNE + kmeans

[tSNE](https://lvdmaaten.github.io/tsne/) plots that we saw before 
when used the __scater__ package are made by using the 
[Rtsne](https://cran.r-project.org/web/packages/Rtsne/index.html) and
[ggplot2](https://cran.r-project.org/web/packages/ggplot2/index.html) packages.
Here we will do the same:


```r
deng <- runTSNE(deng)
plotTSNE(deng)
```

<div class="figure" style="text-align: center">
<img src="06-biological-analyses_files/figure-html/clust-tsne-1.png" alt="tSNE map of the patient data" width="90%" />
<p class="caption">(\#fig:clust-tsne)tSNE map of the patient data</p>
</div>

Note that all points on the plot above are black. This is different 
from what we saw before, when the cells were coloured based on the 
annotation. 

**Here we do not have any annotation and all cells come from the same batch, therefore all dots are black.**

Now we are going to apply _k_-means clustering algorithm to the cloud
of points on the tSNE map. How many groups do you see in the cloud?

We will start with $k=8$:

```r
colData(deng)$tSNE_kmeans <- as.character(kmeans(reducedDim(deng, "TSNE"), centers = 8)$clust)
deng <- runTSNE(deng)
plotTSNE(deng, colour_by = "tSNE_kmeans")
```

```
## Warning: 'add_ticks' is deprecated.
## Use '+ geom_rug(...)' instead.
```

<div class="figure" style="text-align: center">
<img src="06-biological-analyses_files/figure-html/clust-tsne-kmeans2-1.png" alt="tSNE map of the patient data with 8 colored clusters, identified by the k-means clustering algorithm" width="90%" />
<p class="caption">(\#fig:clust-tsne-kmeans2)tSNE map of the patient data with 8 colored clusters, identified by the k-means clustering algorithm</p>
</div>

As you may have noticed, `tSNE+kmeans` is stochastic
and give different results every time it's are run. To get a better
overview of the solutions, we need to run the methods multiple times.
`SC3` is also stochastic, but thanks to the consensus step, it is 
more robust and less likely to produce different outcomes.



## Feature Selection

scRNA-seq is capable of measuring the expression of many
thousands of genes in every cell. However, in most situations only a
portion of those will show a response to the biological condition of
interest, e.g. differences in cell-type, drivers of differentiation,
respond to an environmental stimulus. Most genes detected in a scRNA-seq
experiment will only be detected at different levels due to technical
noise. One consequence of this is that technical noise and batch
effects can obscure the biological signal of interest.

Thus, it is often advantageous to perform feature selection to remove
those genes which only exhibit technical noise from downstream analysis.
Not only does this generally increase the signal:noise ratio in the data;
it also reduces the computational complexity of analyses, by reducing
the total amount of data to be processed.

For scRNA-seq data, we typically focus on unsupervised methods of feature
selection, which don't require any a priori information, such as cell-type
labels or biological group, since they are not available, or may be unreliable,
for many experiments. In contrast, differential expression
can be considered a form of supervised feature selection since it uses the
known biological label of each sample to identify features (i.e. genes) which
are expressed at different levels across groups.

In this section, we will briefly cover the concepts of feature selection, but
due to a limit in time, I refer you to the main coures page for more details
on feature selection: 

http://hemberg-lab.github.io/scRNA.seq.course/biological-analysis.html#feature-selection


### Identifying Genes vs a Null Model

There are two main approaches to unsupervised feature selection. The
first is to identify genes which behave differently from a null model
describing just the technical noise expected in the dataset.

If the dataset contains spike-in RNAs they can be used to directly model
technical noise. However, measurements of spike-ins may not experience
the same technical noise as endogenous transcripts [(Svensson et al., 2017)](https://www.nature.com/nmeth/journal/v14/n4/full/nmeth.4220.html).
In addition, scRNASeq experiments often contain only a small number of
spike-ins which reduces our confidence in fitted model parameters.

#### Highly Variable Genes

The first method proposed to identify features in scRNASeq datasets
was to identify highly variable genes (HVG). HVG assumes that if genes
have large differences in expression across cells some of those differences
are due to biological difference between the cells rather than technical noise.
However, because of the nature of count data, there is a positive relationship
between the mean expression of a gene and the variance in the read counts across
cells. This relationship must be corrected for to properly identify HVGs.

For example, you can use the `BiocGenerics::rowMeans()` and `MatrixStats::rowVars()` 
functions to plot the relationship between mean expression and variance 
for all genes in this dataset. Also, you might try using `log="xy"` to plot 
on a log-scale).

Alternatively, you can use the `trendVar()` and `decomposeVar()` functions
in the `scran` R/Bioconductor package to identify highly variable genes. 

For this section, we go back to the `tung` (or `umi.qc`) data that
has been normalized and batch corrected. For purposes of the tutorial,
we will select the `glm_indi` normalized data. 


```r
umi.qc <- readRDS("data/tung/umi_qc.RDS")

var.fit <- trendVar(umi.qc, method = "loess", assay.type = "glm_indi")
var.out <- decomposeVar(umi.qc, var.fit)
head(var.out)
```

```
## DataFrame with 6 rows and 6 columns
##                               mean             total                 bio
##                          <numeric>         <numeric>           <numeric>
## ENSG00000237683   0.24662736578077 0.243858690202493  0.0356801534015803
## ENSG00000187634 0.0358302498678336 0.041042573799668 0.00447500876958429
## ENSG00000188976   1.71485831826354 0.745273783518252   0.114898539528563
## ENSG00000187961  0.218414586619208 0.203530230554452  0.0193960410083338
## ENSG00000187608    1.3692218193445 0.801170759584193   0.142065201109407
## ENSG00000188157   2.02030076886988 0.545656338513709 -0.0424745356608807
##                               tech              p.value
##                          <numeric>            <numeric>
## ENSG00000237683  0.208178536800913  0.00153459146498894
## ENSG00000187634 0.0365675650300837   0.0157736562230252
## ENSG00000188976  0.630375243989689  0.00085174073496591
## ENSG00000187961  0.184134189546118   0.0312433209044445
## ENSG00000187608  0.659105558474786 0.000120336074819414
## ENSG00000188157  0.588130874174589    0.906968345769253
##                                 FDR
##                           <numeric>
## ENSG00000237683  0.0190971382309735
## ENSG00000187634   0.106015932367908
## ENSG00000188976  0.0124082937456012
## ENSG00000187961   0.167734905189692
## ENSG00000187608 0.00271289057563897
## ENSG00000188157                   1
```

Next, we can plot the mean-variance relationship. The red points are 
the control genes (ERCC and MT genes) that are used to estimate the 
technical variation. The endogenous genes are plotted in black. 


```r
plot(var.out$mean, var.out$total, pch=16, cex=0.6, xlab="Mean log-expression",
    ylab="Variance of log-expression")
points(var.fit$mean, var.fit$var, col="red", pch=16)
o <- order(var.out$mean)
lines(var.out$mean[o], var.out$tech[o], col="red", lwd=2)
```

<img src="06-biological-analyses_files/figure-html/unnamed-chunk-15-1.png" width="90%" style="display: block; margin: auto;" />

Then, the highly variable genes (HVG) are identified as genes
with large positive biological components. 


```r
hvg.out <- var.out[which(var.out$FDR <= 0.05),]
hvg.out <- hvg.out[order(hvg.out$bio, decreasing=TRUE),]
head(hvg.out)
```

```
## DataFrame with 6 rows and 6 columns
##                             mean            total              bio
##                        <numeric>        <numeric>        <numeric>
## ENSG00000106153 2.58010587365124 4.50251391535177 3.99819997557498
## ENSG00000125148 1.10385481967694 2.56632990001896 1.92729406297511
## ENSG00000198865 2.43296165351555 2.36687881721125 1.84095133880717
## ENSG00000110713 3.07884927706287 2.20353806082695 1.76355959764117
## ENSG00000022556 1.87087512946706 2.20770425606636 1.60139777768658
## ENSG00000177105 1.45436561173197 2.03125271629969 1.36969341922523
##                              tech               p.value
##                         <numeric>             <numeric>
## ENSG00000106153 0.504313939776785                     0
## ENSG00000125148 0.639035837043843 1.94361011485433e-234
## ENSG00000198865 0.525927478404086 2.74586525446619e-287
## ENSG00000110713  0.43997846318578                     0
## ENSG00000022556 0.606306478379779 5.88519208672335e-195
## ENSG00000177105 0.661559297074457  7.9450415594479e-138
##                                   FDR
##                             <numeric>
## ENSG00000106153                     0
## ENSG00000125148 5.44210832159212e-231
## ENSG00000198865 1.28140378541756e-283
## ENSG00000110713                     0
## ENSG00000022556 1.17703841734467e-191
## ENSG00000177105 9.26921515268922e-135
```

These are most highly variable genes after normalization and batch correction. 

```r
nrow(hvg.out)
```

```
## [1] 1561
```

We can check the distribution of expression values for the top 10 HVGs
to ensure that they are not being driven by outliers. Here are the 
violin plots of normalized log-expression values for the top 10 HVGs
in the brain dataset.


```r
plotExpression(umi.qc, rownames(hvg.out)[1:10], jitter="jitter", 
               exprs_values = "glm_indi")
```

<img src="06-biological-analyses_files/figure-html/unnamed-chunk-18-1.png" width="90%" style="display: block; margin: auto;" />

Another approach was proposed by 
[Brennecke et al.](http://www.nature.com/nmeth/journal/v10/n11/full/nmeth.2645.html).
To use the Brennecke method, we first normalize for library size then calculate
the mean and the square coefficient of variation (variation divided by
the squared mean expression). A quadratic curve is fit to the relationship
between these two variables for the ERCC spike-in, and then a chi-square 
test is used to find genes significantly above the curve. 

#### High Dropout Genes

An alternative to finding HVGs is to identify genes with 
unexpectedly high numbers of zeros. The frequency of zeros, known 
as the "dropout rate", is very closely related to expression level
in scRNA-seq data. Zeros are the dominant feature of scRNA-seq data, 
typically accounting for over half of the entries in the final expression
matrix. These zeros predominantly result from the failure of mRNAs 
failing to be reversed transcribed [(Andrews and Hemberg, 2016)](http://www.biorxiv.org/content/early/2017/05/25/065094). 


### Correlated Expression

A completely different approach to feature selection is to use 
gene-gene correlations. This method is based on the idea that multiple
genes will be differentially expressed between different cell-types
or cell-states. Genes which are expressed in the same cell-population 
will be positively correlated with each other where as genes expressed 
in different cell-populations will be negatively correated with
each other. Thus important genes can be identified by the magnitude 
of their correlation with other genes.

The limitation of this method is that it assumes technical noise is 
random and independent for each cell, thus shouldn't produce gene-gene 
correlations, but this assumption is violated by batch effects which are
generally systematic between different experimental batches and will
produce gene-gene correlations. As a result it is more appropriate to
take the top few thousand genes as ranked by gene-gene correlation than
consider the significance of the correlations.

Lastly, another common method for feature selection in scRNA-seq data 
is to use PCA loadings. Genes with high PCA loadings are likely to be
highly variable and correlated with many other variable genes, thus
may be relevant to the underlying biology. However, as with gene-gene 
correlations PCA loadings tend to be susceptible to detecting systematic 
variation due to batch effects; thus it is recommended to plot the PCA
results to determine those components corresponding to the biological 
variation rather than batch effects.



## Differential Expression (DE) analysis 

### Bulk RNA-seq

One of the most common types of analyses when working with bulk RNA-seq
data is to identify differentially expressed genes. By comparing the
genes that change between two conditions, e.g. mutant and wild-type or
stimulated and unstimulated, it is possible to characterize the
molecular mechanisms underlying the change.

Several different methods,
e.g. [DESeq2](https://bioconductor.org/packages/DESeq2) and
[edgeR](https://bioconductor.org/packages/release/bioc/html/edgeR.html),
have been developed for bulk RNA-seq. Moreover, there are also
extensive
[datasets](http://genomebiology.biomedcentral.com/articles/10.1186/gb-2013-14-9-r95)
available where the RNA-seq data has been validated using
RT-qPCR. These data can be used to benchmark DE finding 
algorithms and the available evidence suggests that the 
algorithms are performing quite well.

### Single-cell RNA-seq

In contrast to bulk RNA-seq, in scRNA-seq we usually do 
not have a defined set of experimental conditions. Instead, 
as was shown in a previous section we can identify the cell
groups by using an unsupervised clustering approach. Once the 
groups have been identified one can find differentially
expressed genes either by comparing the differences in variance
between the groups (like the Kruskal-Wallis test implemented in
SC3), or by comparing gene expression between clusters in a 
pairwise manner. In the following section we will mainly 
consider tools developed for pairwise comparisons.

### Differences in distribution

Unlike bulk RNA-seq, we generally have a large number of 
samples (i.e. cells) for each group we are comparing in single-cell 
experiments. Thus we can take advantage of the whole distribution
of expression values in each group to identify differences 
between groups rather than only comparing estimates of 
mean-expression as is standard for bulk RNA-seq.

There are two main approaches to comparing distributions. 
Firstly, we can use existing statistical models/distributions 
and fit the same type of model to the expression in each 
group then test for differences in the parameters for each 
model, or test whether the model fits better if a particular
paramter is allowed to be different according to group. For
instance in section on dealing with confounders, we used 
`edgeR` to test whether allowing mean expression to be different 
in different batches significantly improved the fit of a 
negative binomial model of the data.

Alternatively, we can use a non-parametric test which does not 
assume that expression values follow any particular distribution, 
e.g. the [Kolmogorov-Smirnov test (KS-test)](https://en.wikipedia.org/wiki/Kolmogorov%E2%80%93Smirnov_test).
Non-parametric tests generally convert observed expression 
values to ranks and test whether the distribution of ranks 
for one group are signficantly different from the distribution 
of ranks for the other group. However, some non-parametric 
methods fail in the presence of a large number of tied values,
such as the case for dropouts (zeros) in single-cell RNA-seq 
expression data. Moreover, if the conditions for a parametric 
test hold, then it will typically be more powerful than a
non-parametric test.

### Models of single-cell RNA-seq data

The most common model of RNASeq data is the negative binomial model: 


```r
set.seed(1)
hist(
    rnbinom(
        1000, 
        mu = 10, 
        size = 100), 
    col = "grey50", 
    xlab = "Read Counts", 
    main = "Negative Binomial"
)
```

<div class="figure" style="text-align: center">
<img src="06-biological-analyses_files/figure-html/nb-plot-1.png" alt="Negative Binomial distribution of read counts for a single gene across 1000 cells" width="90%" />
<p class="caption">(\#fig:nb-plot)Negative Binomial distribution of read counts for a single gene across 1000 cells</p>
</div>
Mean:
$\mu = mu$

Variance:
$\sigma^2 = mu + mu^2/size$

It is parameterized by the mean expression (mu) and the dispersion (size), 
which is inversely related to the variance. The negative binomial model 
fits bulk RNA-seq data very well and it is used for most statistical 
methods designed for such data. In addition, it has been show to fit 
the distribution of molecule counts obtained from data tagged by unique 
molecular identifiers (UMIs) quite well
([Grun et al. 2014](http://www.nature.com/nmeth/journal/v11/n6/full/nmeth.2930.html), 
[Islam et al. 2011](http://genome.cshlp.org/content/21/7/1160)).

However, a raw negative binomial model does not fit full-length
transcript data as well due to the high dropout rates relative 
to the non-zero read counts. For this type of data a variety of
zero-inflated negative binomial models have been proposed (e.g.
[MAST](https://bioconductor.org/packages/release/bioc/html/MAST.html),
[SCDE](https://bioconductor.org/packages/release/bioc/html/scde.html)).


```r
d <- 0.5;
counts <- rnbinom(
    1000, 
    mu = 10, 
    size = 100
)
counts[runif(1000) < d] <- 0
hist(
    counts, 
    col = "grey50", 
    xlab = "Read Counts", 
    main = "Zero-inflated NB"
)
```

<div class="figure" style="text-align: center">
<img src="06-biological-analyses_files/figure-html/zero-inflation-plot-1.png" alt="Zero-inflated Negative Binomial distribution" width="90%" />
<p class="caption">(\#fig:zero-inflation-plot)Zero-inflated Negative Binomial distribution</p>
</div>
Mean:
$\mu = mu \cdot (1 - d)$

Variance:
$\sigma^2 = \mu \cdot (1-d) \cdot (1 + d \cdot \mu + \mu / size)$

These models introduce a new parameter $d$, for the dropout rate, 
to the negative binomial model. Turns out, the dropout rate of a gene
is strongly correlated with the mean expression of the gene. Different
zero-inflated negative binomial models use different relationships 
between $\mu$ and $d$ and some may fit $\mu$ and $d$ to the expression
of each gene independently.



## DE in `tung` dataset


```r
library(scRNA.seq.funcs)
library(edgeR)
library(ROCR)
```

### Introduction

To test different single-cell differential expression methods, 
we will continue using the `tung` dataset. For this experiment 
bulk RNA-seq data for each cell-line was generated in addition 
to single-cell data. We will use the differentially expressed genes
identified using standard methods on the respective bulk data 
as the ground truth for evaluating the accuracy of each single-cell
method. To save time we have pre-computed these for you. You can
run the commands below to load these data.


```r
DE <- read.table("data/tung/TPs.txt")
notDE <- read.table("data/tung/TNs.txt")
GroundTruth <- list(
    DE = as.character(unlist(DE)), 
    notDE = as.character(unlist(notDE))
)
```

This ground truth has been produce for the comparison of 
individual `NA19101` to `NA19239`. 


```r
umi.qc.sub <- umi.qc[, umi.qc$individual %in% c("NA19101", "NA19239")]
group <- colData(umi.qc.sub)$individual
batch <- colData(umi.qc.sub)$batch
norm_data <- assay(umi.qc.sub, "glm_indi")
```

Now we will compare various single-cell DE methods. 
Note that we will only be running methods which are 
available as R-packages and run relatively quickly.

### Kolmogorov-Smirnov test

The types of test that are easiest to work with are 
non-parametric ones. The most commonly used non-parametric
test is the
[Kolmogorov-Smirnov test](https://en.wikipedia.org/wiki/Kolmogorov%E2%80%93Smirnov_test) 
(KS-test) and we can use it to compare the distributions
for each gene in the two individuals.

The KS-test quantifies the distance between the empirical 
cummulative distributions of the expression of each gene
in each of the two populations. It is sensitive to changes 
in mean experession and changes in variability. However
it assumes data is continuous and may perform poorly
when data contains a large number of identical values 
(e.g. zeros). Another issue with the KS-test is that it can
be very sensitive for large sample sizes and thus it may
end up as significant even though the magnitude of the
difference is very small.

<div class="figure" style="text-align: center">
<img src="figures/KS2_Example.png" alt="Illustration of the two-sample Kolmogorov–Smirnov statistic. Red and blue lines each correspond to an empirical distribution function, and the black arrow is the two-sample KS statistic. (taken from [here](https://en.wikipedia.org/wiki/Kolmogorov%E2%80%93Smirnov_test))" width="60%" />
<p class="caption">(\#fig:ks-statistic)Illustration of the two-sample Kolmogorov–Smirnov statistic. Red and blue lines each correspond to an empirical distribution function, and the black arrow is the two-sample KS statistic. (taken from [here](https://en.wikipedia.org/wiki/Kolmogorov%E2%80%93Smirnov_test))</p>
</div>

Now run the test:


```r
pVals <- apply(
    norm_data, 1, function(x) {
        ks.test(
            x[group == "NA19101"], 
            x[group == "NA19239"]
        )$p.value
    }
)
# multiple testing correction
pVals <- p.adjust(pVals, method = "fdr")
```
This code "applies" the function to each row (specified by 1)
of the expression matrix, data. In the function, we are 
returning just the `p.value` from the `ks.test()` output. 
We can now consider how many of the ground truth positive
and negative DE genes are detected by the KS-test. 

#### Evaluating Accuracy


```r
sigDE <- names(pVals)[pVals < 0.05]

# Number of KS-DE genes
length(sigDE) 
```

```
## [1] 11942
```

```r
# Number of KS-DE genes that are true DE genes
sum(GroundTruth$DE %in% sigDE) 
```

```
## [1] 989
```

```r
# Number of KS-DE genes that are truly not-DE
sum(GroundTruth$notDE %in% sigDE)
```

```
## [1] 8527
```

As you can see many more of our ground truth negative genes
were identified as DE by the KS-test (false positives) than
ground truth positive genes (true positives), however this 
may be due to the larger number of notDE genes thus we typically
normalize these counts as the True positive rate (TPR), TP/(TP + FN),
and False positive rate (FPR), FP/(FP+TP).


```r
tp <- sum(GroundTruth$DE %in% sigDE)
fp <- sum(GroundTruth$notDE %in% sigDE)
tn <- sum(GroundTruth$notDE %in% names(pVals)[pVals >= 0.05])
fn <- sum(GroundTruth$DE %in% names(pVals)[pVals >= 0.05])
tpr <- tp/(tp + fn)
fpr <- fp/(fp + tn)
cat(c(tpr, fpr))
```

```
## 0.9347826 0.8212463
```
Now we can see the TPR is much higher than the FPR 
indicating the KS test is identifying DE genes.

So far we've only evaluated the performance at a single 
significance threshold. Often it is informative to vary 
the threshold and evaluate performance across a range of 
values. This is then plotted as a receiver-operating-characteristic
curve (ROC) and a general accuracy statistic can be calculated 
as the area under this curve (AUC). We will use the `ROCR` package
to facilitate this plotting.


```r
# Only consider genes for which we know the ground truth
pVals <- pVals[names(pVals) %in% GroundTruth$DE | 
               names(pVals) %in% GroundTruth$notDE] 
truth <- rep(1, times = length(pVals));
truth[names(pVals) %in% GroundTruth$DE] = 0;
pred <- ROCR::prediction(pVals, truth)
perf <- ROCR::performance(pred, "tpr", "fpr")
ROCR::plot(perf)
```

<div class="figure" style="text-align: center">
<img src="06-biological-analyses_files/figure-html/ks-roc-plot-1.png" alt="ROC curve for KS-test." width="90%" />
<p class="caption">(\#fig:ks-roc-plot)ROC curve for KS-test.</p>
</div>

```r
aucObj <- ROCR::performance(pred, "auc")
aucObj@y.values[[1]] # AUC
```

```
## [1] 0.6717898
```
Finally, to facilitate the comparisons of other DE methods, 
let's put this code into a function so we don't need to repeat it:


```r
DE_Quality_AUC <- function(pVals) {
    pVals <- pVals[names(pVals) %in% GroundTruth$DE | 
                   names(pVals) %in% GroundTruth$notDE]
    truth <- rep(1, times = length(pVals));
    truth[names(pVals) %in% GroundTruth$DE] = 0;
    pred <- ROCR::prediction(pVals, truth)
    perf <- ROCR::performance(pred, "tpr", "fpr")
    ROCR::plot(perf)
    aucObj <- ROCR::performance(pred, "auc")
    return(aucObj@y.values[[1]])
}
```

### Wilcox/Mann-Whitney-U Test

The Wilcox-rank-sum test is another non-parametric test, but
tests specifically if values in one group are greater/less 
than the values in the other group. Thus it is often 
considered a test for difference in median expression
between two groups; whereas the KS-test is sensitive 
to any change in distribution of expression values.


```r
pVals <- apply(
    norm_data, 1, function(x) {
        wilcox.test(
            x[group == "NA19101"], 
            x[group == "NA19239"]
        )$p.value
    }
)
# multiple testing correction
pVals <- p.adjust(pVals, method = "fdr")
DE_Quality_AUC(pVals)
```

<div class="figure" style="text-align: center">
<img src="06-biological-analyses_files/figure-html/wilcox-plot-1.png" alt="ROC curve for Wilcox test." width="90%" />
<p class="caption">(\#fig:wilcox-plot)ROC curve for Wilcox test.</p>
</div>

```
## [1] 0.6779454
```

### edgeR

edgeR is based on a negative binomial model of gene expression and 
uses a generalized linear model (GLM) framework, the enables us
to include other factors such as batch to the model.


```r
dge <- DGEList(
    counts = assay(umi.qc.sub, "counts"), 
    norm.factors = rep(1, length(assay(umi.qc.sub, "counts")[1,])), 
    group = group
)
group_edgeR <- factor(group)
design <- model.matrix(~ group_edgeR)
dge <- estimateDisp(dge, design = design, trend.method = "none")
fit <- glmFit(dge, design)
res <- glmLRT(fit)
pVals <- res$table[,4]
names(pVals) <- rownames(res$table)

pVals <- p.adjust(pVals, method = "fdr")
DE_Quality_AUC(pVals)
```

### Monocle

[Monocle](https://bioconductor.org/packages/release/bioc/html/monocle.html) can use several different models for DE. For count data it recommends the Negative Binomial model (negbinomial.size). For normalized data it recommends log-transforming it then using a normal distribution (gaussianff). Similar to edgeR this method uses a GLM framework so in theory can account for batches, however in practice the model fails for this dataset if batches are included.


### MAST

[MAST](https://bioconductor.org/packages/release/bioc/html/MAST.html) is based on a zero-inflated negative binomial model. It tests for differential expression using a hurdle model to combine tests of discrete (0 vs not zero) and continuous (non-zero values) aspects of gene expression. Again this uses a linear modelling framework to enable complex models to be considered.


### SCDE
[SCDE](http://hms-dbmi.github.io/scde/) is the first single-cell specific DE method. It fits a zero-inflated negative binomial model to expression data using Bayesian statistics. The usage below tests for differences in mean expression of individual genes across groups but recent versions include methods to test for differences in mean expression or dispersion of groups of genes, usually representing a pathway.


### zinbwave + DESeq2


```r
library(zinbwave)

# low count filter - at least 25 samples with count of 5 or more
keep <- rowSums(counts(umi.qc.sub) > 0) > 5
table(keep)
zinb <- umi.qc.sub[keep,]
zinb$individual <-  colData(umi.qc.sub)$individual

# we need to reorganize the assays in the SumExp from splatter
nms <- c("counts", setdiff(assayNames(zinb), "counts"))
assays(zinb) <- assays(zinb)["counts"] # c("counts", "glm_indi")]
# epsilon setting as recommended by the ZINB-WaVE integration paper
# system.time({
  zinb <- zinbwave(zinb, K=0, BPPARAM=SerialParam(), epsilon=1e12)
# })
```

Van den Berge and Perraudeau and others have shown the LRT may perform
better for null hypothesis testing, so we use the LRT. In order to use
the Wald test, it is recommended to set `useT=TRUE`.


```r
suppressPackageStartupMessages(library(DESeq2))
dds <- DESeqDataSet(zinb, design=~individual)
dds <- DESeq(dds, test="LRT", reduced=~1,
               sfType="poscounts", minmu=1e-6, minRep=Inf)
```