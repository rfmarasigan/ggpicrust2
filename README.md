
<!-- README.md is generated from README.Rmd. Please edit that file -->

# ggpicrust2

ggpicrust2 is a comprehensive package that integrates pathway
name/description annotations, ten of the most advanced differential
abundance (DA) methods, and visualization of DA results. It offers a
comprehensive solution for analyzing and interpreting the results of
picrust2 functional prediction in a seamless and intuitive way. Whether
you are a researcher, data scientist, or bioinformatician, ggpicrust2
can help you better understand the underlying biological processes and
mechanisms at play in your picrust2 output data. So if you are
interested in exploring the output data of picrust2, ggpicrust2 is the
tool you need.

## Citation

If you use ggpicrust2 in your research, please cite the following paper:

Chen Yang, Aaron Burberry, Jiahao Mai, Liangliang Zhang. (2023).
[ggpicrust2: an R package for PICRUSt2 predicted functional profile
analysis and visualization.](https://arxiv.org/abs/2303.10388) arXiv
preprint arXiv:2303.10388.

BibTeX entry: @misc{yang2023ggpicrust2, title={ggpicrust2: an R package
for PICRUSt2 predicted functional profile analysis and visualization},
author={Chen Yang and Aaron Burberry and Jiahao Mai and Liangliang
Zhang}, year={2023}, eprint={2303.10388}, archivePrefix={arXiv},
primaryClass={stat.AP} }

## Installation

You can install the stable version of ggpicrust2 from CRAN with:

``` r
install.packages("ggpicrust2")
```

To install the latest development version of ggpicrust2 from GitHub, you
can use:

``` r
# install.packages("devtools")
devtools::install_github("cafferychen777/ggpicrust2")
```

## Stay Updated

Follow me on Twitter for the latest ggpicrust2 updates:
[![](https://img.shields.io/twitter/follow/CafferyYang?style=social)](https://twitter.com/CafferyYang)

## Workflow

The easiest way to analyze the picrust2 output is using ggpicrust2()
function. The entire pipeline can be run with ggpicrust2() function.

ggpicrust2() integrates ko abundance to kegg pathway abundance
conversion, annotation of pathway, differential abundance (DA) analysis,
DA results visualization.

![](https://cafferychen777.github.io/ggpicrust2/reference/figures/ggpicrust2%20fig1.jpeg)

![](https://cafferychen777.github.io/ggpicrust2/reference/figures/ggpicrust2%20fig2.jpeg)

``` r
#If you want to analysis kegg pathway abundance instead of ko within the pathway. You should turn ko_to_kegg to TRUE.
#The kegg pathway typically have the more explainable description.
metadata <-
  read_delim(
    "~/Microbiome/C9orf72/Code And Data/new_metadata.txt",
    delim = "\t",
    escape_double = FALSE,
    trim_ws = TRUE
  )
group <- "Enviroment"
daa_results_list <-
  ggpicrust2(
    file = "/Users/apple/Microbiome/C9orf72/Code And Data/picrust2_out/KO_metagenome_out/pred_metagenome_unstrat.tsv/pred_metagenome_unstrat.tsv",
    metadata = metadata,
    group = "Enviroment",
    pathway = "KO",
    daa_method = "LinDA",
    order = "pathway_class",
    ko_to_kegg = TRUE,
    x_lab = "pathway_name",
    p.adjust = "BH",
    select = NULL,
    reference = NULL
  )
#The visualization will be published in viewer.

#If you want to analysis the EC. MetaCyc. KO without conversions. You should turn ko_to_kegg to FALSE.
metadata <-
  read_delim(
    "~/Microbiome/C9orf72/Code And Data/new_metadata.txt",
    delim = "\t",
    escape_double = FALSE,
    trim_ws = TRUE
  )
group <- "Enviroment"
daa_results_list <-
  ggpicrust2(
    file = "//Users/apple/Microbiome/C9orf72/Code And Data/picrust2_out/EC_metagenome_out/pred_metagenome_unstrat.tsv/pred_metagenome_unstrat.tsv",
    metadata = metadata,
    group = "Enviroment",
    pathway = "EC",
    daa_method = "LinDA",
    order = "group",
    ko_to_kegg = FALSE,
    x_lab = "description",
    p.adjust = "BH",
    select = NULL,
    reference = NULL
  )
#The visualization will be published in viewer.
```

## Output

The typical output of the ggpicrust2 is like this.

![](https://cdn.jsdelivr.ren/gh/cafferychen777/ggpicrust2_paper@main/paper_figure/figure1.jpg)

## function details

### ko2kegg_abundance()

KEGG Orthology(KO) is a classification system developed by the Kyoto
Encyclopedia of Genes and Genomes (KEGG) data-base(Kanehisa et al.,
2022). It uses a hierarchical structure to classify enzymes based on the
reactions they catalyze. To better understand pathways’ role in
different groups and classify the pathways, the KO abundance table needs
to be converted to KEGG pathway abundance. But PICRUSt2 removes the
function from PICRUSt. ko2kegg_abundance() can help convert the table.

``` r
# Sample usage of the ko2kegg_abundance function

# Assume that the KO abundance table is stored in a file named "ko_abundance.tsv"

ko_abundance_file <- "ko_abundance.tsv"

kegg_abundance <- ko2kegg_abundance(ko_abundance_file)

# The resulting kegg_abundance data frame can now be used for further analysis and visualization.
```

### pathway_daa()

Differential abundance(DA) analysis plays a major role in PICRUSt2
downstream analysis. pathway_daa() integrates almost all DA methods
applicable to the predicted functional profile which there excludes
ANCOM and ANCOMBC. It includes ALDEx2(Fernandes et al., 2013),
DEseq2(Love et al., 2014), Maaslin2(Mallick et al., 2021), Lin-DA(Zhou
et al., 2022), edgeR(Robinson et al., 2010) , limma voom(Ritchie et al.,
2015), metagenomeSeq(Paulson et al., 2013), lefser(Segata et al., 2011).

``` r
#the abundance table is better to be data.frame rather than tibble
#you can use ko2_kegg_abundance output
abundance <- ko2kegg_abundance(ko_abundance_file)
metadata <-
  read_delim(
    "~/Microbiome/C9orf72/Code And Data/new_metadata.txt",
    delim = "\t",
    escape_double = FALSE,
    trim_ws = TRUE
  )
group <- "Enviroment"
#daa_method default is "ALDex2"
daa_results_df <- pathway_daa(abundance = abundance,
           metadata = metadata,
           group = group ,
           daa_method = "limma voom",
           select = NULL,
           p.adjust = "BH",
           reference = NULL)
#If you group levels >3 and want to use the LinDA or limma voom, you should give a reference.
metadata <-
  read_delim(
    "~/Microbiome/C9orf72/Code And Data/new_metadata.txt",
    delim = "\t",
    escape_double = FALSE,
    trim_ws = TRUE
  )
group <- "Group"
daa_results_df <- pathway_daa(abundance = abundance,
           metadata = metadata,
           group = group ,
           daa_method = "limma voom",
           select = NULL,
           p.adjust = "BH",
           reference = "Harvard BRI")
```

### pathway_annotation

``` r
daa_results_df <- pathway_annotation(pathway = "KO", daa_results_df = daa_results_df, ko_to_kegg = TRUE)
```

### pathway_errorbar

``` r
pathway_errorbar(abundance = abundance,
           daa_results_df = daa_results_df,
           Group = metadata$Enviroment,
           ko_to_kegg = TRUE,
           p_values_threshold = 0.05,
           order = "pathway_class",
           select = NULL,
           p_value_bar = TRUE,
           colors = NULL,
           x_lab = NULL)
```

### pathway_heatmap

In this section, we will demonstrate how to create a pathway heatmap
using the `pathway_heatmap` function in the ggpicrust2 package. This
function visualizes the relative abundance of pathways in different
samples.

First, we will create some example functional pathway abundance data and
metadata:

``` r
# Create example functional pathway abundance data
abundance_example <- matrix(rnorm(30), nrow = 10, ncol = 3)
rownames(abundance_example) <- paste0("Sample", 1:10)
colnames(abundance_example) <- c("PathwayA", "PathwayB", "PathwayC")

# Create example metadata
# Please change your sample id's column name to sample_name
metadata_example <- data.frame(sample_name = rownames(abundance_example),
                               group = factor(rep(c("Control", "Treatment"), each = 5)))
```

Next, we will use the pathway_heatmap function to create a heatmap:

``` r
# Create a heatmap
heatmap_plot <- ggpicrust2::pathway_heatmap(t(abundance_example), metadata_example, "group")
```

Finally, we will display the resulting heatmap:

``` r
print(heatmap_plot)
```

### pathway_pca()

In this section, we will demonstrate how to perform Principal Component
Analysis (PCA) on functional pathway abundance data and create
visualizations of the PCA results using the `pathway_pca` function in
the ggpicrust2 package.

First, we will create some example functional pathway abundance data and
metadata:

``` r
# Create example functional pathway abundance data
abundance_example <- data.frame(A = rnorm(10), B = rnorm(10), C = rnorm(10))

# Create example metadata
metadata_example <- tibble::tibble(sample_id = 1:10,
                                   group = factor(rep(c("Control", "Treatment"), each = 5)))
```

Next, we will use the pathway_pca function to perform PCA and create
visualizations:

``` r
# Perform PCA and create visualizations
pca_plot <- ggpicrust2::pathway_pca(t(abundance_example), metadata_example, "group")
```

Finally, we will display the resulting PCA plot:

``` r
print(pca_plot)
```
