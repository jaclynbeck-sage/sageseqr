<!-- badges: start -->
  [![R build status](https://github.com/kelshmo/sageseqr/workflows/R-CMD-check/badge.svg)](https://github.com/kelshmo/sageseqr/actions)
<!-- badges: end -->
# Installation 

Install the package with:
```
remotes::install_github("Sage-Bionetworks/sageseqr")
```
or
```
devtools::install_github("Sage-Bionetworks/sageseqr")
```

**Note:** If installation fails with this error:
```
ERROR: dependency ‘synapser’ is not available for package 'sageseqr'
```
you can solve this with the following:
```
reticulate::virtualenv_create('r-reticulate')
install.packages("synapser", repos = "http://ran.synapse.org")
```
Then try installing sageseqr again.

# RNA-seq normalization workflow in R

The `sageseqr` package integrates the [`targets` R package](https://github.com/ropensci/targets/), the [`config` package for R](https://cran.r-project.org/web/packages/config/vignettes/introduction.html), and [Synapse](https://www.synapse.org/). `targets` tracks dependency relationships in the workflow and only updates data when it has changed. A `config` file allows inputs and parameters to be explicitly defined in one location. Synapse is a data repository that allows sensitive data to be [stored and shared responsibly](https://docs.synapse.org/articles/article_index.html#governance). 

The workflow takes RNA-seq gene counts and sample metadata as inputs, normalizes counts by conditional quantile normalization [(CQN)](https://bioconductor.org/packages/release/bioc/html/cqn.html), removes outliers based on a user-defined threshold, empirically selects meaningful covariates and returns differential expression analysis results. The data is also visualized in several ways to help you understand meaningful trends. The visualizations include a heatmap identifying highly correlated covariates, a sample-specific x and y marker gene check, boxplots visualizing the distribution of continuous variables and a principal component analysis (PCA) to visualize sample distribution.

# The Targets

The series of steps that make up the workflow are called targets. The target objects are stored in a cache and can either be read or loaded into your environment with the `targets` functions `tar_read` or `tar_load`. Source code for each target can be visualized by setting `show_source = TRUE` with `loadd` and `readd`. 

Importantly, running `clean` will remove the data stored as targets ([but, the data is never completely gone!](https://books.ropensci.org/targets/walkthrough.html)). You may specific targets by name by passing them to the `tar_destroy()` function.

The targets are called by the `targets` `tar_make()` function and are:

Raw data: 
- `import_metadata`- imports the raw metadata directly from synapse
- `import_counts` - imports the raw counts directly from synapse
- `biomart_results` - the complete list of genes with biomaRt annotations.

Exploratory data visualizations:
- `gene_coexpression` - the distribution of correlated gene counts.
- `boxplots` - the distribution of continuous variables.
- `sex_plot` - the distribution of samples by x and y marker genes.
- `sex_plot_pca` - a PCA of sex-specific expression to visualize more 
                   dimensionality than `sex_plot`.
- `correlation_plot` - the correlation of covariates.
- `significant_covariates_plot` - the correlation of covariates to gene 
                                  expression.
- `outliers` - the clustering of samples by PCA.
- `plot_de_volcano` - volcano plot of differentially expressed genes.

Transformed or normalized data:
- `clean_md` - metadata with factor and numeric types.
- `filtered_counts` - counts matrix with low gene expression removed.
- `biotypes` - gene proportions summarized by biotype.
- `cqn_counts` - CQN normalized counts. 
- `model` - model selected by multivariate forward stepwise regression 
            (evaluated by Bayesian Information Criteria (BIC)).
- `de` - differential expression results including adjusted p-values and gene list.
- `report` - output markdown report rendered as HTML.

# Access to Data 

Anyone can create a [Synapse account](https://docs.synapse.org/articles/getting_started.html) and access public data in a variety of disciplines: [Alzheimer's Disease Knowledge portal](https://adknowledgeportal.synapse.org/), [CommonMind Consoritum](https://www.synapse.org/#!Synapse:syn2759792/wiki/69613).   
