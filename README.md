# WormBase single cell tools

This page is a showcase of ideas for single cell tools for WormBase (and eventually the [Alliance](https://www.alliancegenome.org/)) that we are working on. These tools are in the final stages of development and made available for testing. If you have feedback you can write to Eduardo at eduardo@wormbase.org.

**scdefg**: Because differential expression between arbitrary groups cannot be pre-computed (there would be too many possibilities) this app performs differential expression on demand using [scvi-tools](https://scvi-tools.org) on the backend.

**wormcells-viz**: For other kinds of visualizations of gene abundance static data, which can be precomputed and then only requires slicing a large table of values to render the results. Currently these static visualizations are heatmaps, dotplots, ridgeline plots, and swarm plots. 

### Talks
- 2021-03-22 [Link to slides](https://docs.google.com/presentation/d/1wQyG6Ww75HRPizOojnGb6N5rx4RH8_DIZXiX83ddD4w/edit?usp=sharing) Plans for WormBase single cell tools [link to presentation video](https://www.youtube.com/watch?v=TymnrF_b59A), single cell discussion starts at 19m30s.

- 2021-06-03. [Link to slides](https://docs.google.com/presentation/d/1Kv4gPsm-wT8wH_5nAd9sFBFlI6dTvZ8O407Tnj3Gv0Q/edit?usp=sharing) Update on development status ahead of preprint and production deploys

Because differential expression between arbitrary groups cannot be pre-computed (there would be too many possibilities),  we created the `scdefg` app for doing it using scvi-tools on the backend.


## scdefg: Interactive differential expression 

The `scdefg` app consists of a single web page initially displaying a short description, a text input of genes to be highlighted in the output volcano plot, and two lists of cell types to be selected. After submission results are displayed in the form of an interactive volcano plot displaying gene descriptions and two sortable tabular views of the p-values and log fold changes of expression levels showing enriched and depleted genes. The tabular results can be exported to csv and Excel format, or copied to the clipboard.

The app takes a pre-trained scVI mode as input. Training an scVI model is usually quick on GPUs and needs to be done only once, while the differential expression analysis performed on the fly by *scdefg* is less computationally intensive and does not necessarily require GPUs. At WormBase, we have deployed the app on an AWS t2.large cloud instance with only 8GB RAM and 2 vCPU. This basic configuration is sufficient for handling a few concurrent users with results being returned within 15s. 
 
 **Status:** Late stage of development, soon to be available on pip and preprint released

**Repository:** [https://github.com/WormBase/scdefg](https://github.com/WormBase/scdefg)

**Deployment:** 
- Current version: http://18.223.180.183:1337/ test deploy with all C. elegans scRNAseq 10x v2/v3 data - [download trained model here](https://github.com/Munfred/wormcells-data/releases/tag/packer2019taylor2020cendavid2021_model) 
- OLD VERSION: [cengen-de](https://www.cengen-de.textpressolab.com/) where you can perform differential expression on the CeNGEN dataset. 


**Current plans:** Release on pip and public deploy with all C. elegans 10x scRNAseq data


## wormcells-viz: Framework for static data

This tool is still in the early stages of development. It is meant to take in a large csv file with the precomputed gene abundance data and then display the user gene and cell type selection in the desired visualization. We have a demonstration deployment that is undergoing rapid development. The plots will be implemented with the [D3.js library](https://www.d3-graph-gallery.com/)

**Repository:**   [https://github.com/WormBase/single-cell-visualization-tools](https://github.com/WormBase/single-cell-visualization-tools)

**Demonstration deployment:** [http://cervino.caltech.edu:3000/](http://cervino.caltech.edu:3000/) 

**Status:** Development of new features is done, the tool is being tested and documentation is being written for a public deploy. 

**Current plans:** 1) Create a common backend framework for data selection (choosing cell types and genes to visualize). 2) Release on pip. 3) Integrate CeNGEN and Packer 2019 data for next deployment. 

### Heatmaps & dot plots  
Visualize mean gene expression across select genes & cell types. Dotplots are similar to heatmaps except instead of colored squares they show the data in the form of circles of different sizes. 

### Ridgeline Gene abundance histograms 
Visualize gene abundances stratified by cell type and experiment

### Swarm plots 
Visualize expression of a gene across all cell types relative to one cell type. These plots are useful for identifying candidate marker genes!

- Y axis: a set of selected genes, evenly spaced
- X axis: the log fold change of expression of that gene on all cell types, relative to the cell of interest. 
- 0 = baseline expression on reference cell type, 
- < 0 means lower expression in that cell type relative to reference
- > 0 means higher expression in cell type relative to reference

A Colab tutorial on how to make swarm plots is available at: https://github.com/Munfred/worm-markers 

# How WormBase processes single cell RNA data: scvi-tools

There are currently hundreds of software tools and pipelines developed for scRNAseq data (see [https://www.scrna-tools.org](scrna-tools.org)). For processing single sell data at WormBase we have chosen to use the [scvi-tools.org](https://scvi-tools.org) framework. scvi-tools is different from most other scRNAseq tools in that it uses [variational autoencoders](https://arxiv.org/abs/1906.02691) to learn the distribution underlying the input data and create a generative model.  Interested readers can learn more in about the framework in the scvi-tools [documentation](https://docs.scvi-tools.org/en/stable/). Here we briefly highlight a few considerations that lead to our choice of using the the framework for driving scRNAseq analysis.

- **Scalability:** Using a GPU, scvi-tools can scale to datasets with millions of cells. These large models can be trained in about an hour.

- **Consistent Development and Contributors:** The scvi-tools codebase [https://github.com/YosefLab/scvi-tools](github.com/YosefLab/scvi-tools) was first introduced in 2017, and published in 2018 by [Lopez et al](https://doi.org/10.1038/s41592-018-0229-2). It currently boasts [35](https://github.com/YosefLab/scvi-tools/graphs/contributors) unique contributors and [52](https://github.com/YosefLab/scvi-tools/releases) releases. 

- **Extensible Framework for Analysis:** Because the generative model of the data (formally, a hierarchical bayesian network) can be modified to reflect our assumptions about underlying processes, the framework can be extended to model other aspects of scRNAseq data. Currently, extensions include cell type classification and label transfer across batches, modelling single cell protein measurements, performing gene imputation in spatial data, and using a linear decoder to allow for interpretation of the learned latent space. Several peer reviewed articles have been published describing these extensions (see [https://scvi-tools.org/press](scvi-tools.org/press)).

# WormBase deployment philosophy for single cell tools

At the moment, the majority of scRNAseq data is generated using the 10X Genomics Chromium technology v2 and v3 chemistry (see [Svensson 2020: A curated database reveals trends in single-cell transcriptomics ](https://doi.org/10.1093/database/baaa073). This is also true for C. elegans scRNAseq data. At least for the time being WormBase will focus development efforts on scRNAseq tools to 10X Genomics data. Two considerations drive this:

- Data integration of different batches with scvi-tools is more robust when there is more data, and when the technology and biological system from the different batches is more  similar. Attempting to integrate a small number of cells from niche technologies and unique biological systems could lead to potentital artifacts and pitfalls and will not be done. For example, the Hashimony 2012 and Tintori 2016 datasets listed below will not be integrated. Niche technologies or small numbers of cells require a case by case treatment,and attempting integration with these datasets could lead to a situation where it is impossible to discern biological differences from technical artifacts.

- The 10X Genomics data has a widely used, validated and commercially supported data pre-processing workflow, from FASTQ files to gene count matrices. As such, if WormBase decides to uniformly reprocess the FASTQ files in the future, it is ideal that the data pre-processing pipeline is the same for all datasets.




# List of C. elegans single cell datasets

Here we provide a collection of all publicly available C. elegans single cell and single nucleus RNA sequencing data. In addition to listing studies and the original data sources, for convenience the data has been repackaged and a direct download link to the data in [.h5ad](https://anndata.readthedocs.io/en/latest/) format is provided. The GitHub repository of this website is [https://github.com/WormBase/single-cell](https://github.com/WormBase/single-cell), and the .h5ad files are hosted as releases [here](https://github.com/Munfred/wormcells-data/releases). If you see a
dataset missing or something incorrect or out of date, please submit an [issue on GitHub](https://github.com/WormBase/single-cell/issues).


## Data wrangling conventions

This is a curated collection of all C. elegans single cell RNA seq high throughput data wrangled into the [anndata](https://anndata.readthedocs.io/en/stable/) format in `.h5ad` files with standard fields, plus any number of optional fields that vary depending on the metadata the authors provide. 

As possible, we attempt to keep the field names lower case, short, descriptive, and only using valid Python variable names so they may be accessed via the syntax `adata.var.field_name` 

The WormBase anndata wrangling convention is describede at [https://github.com/WormBase/anndata-wrangling](https://github.com/WormBase/anndata-wrangling)

<font size="1" face="Arial">
<table style="margin-left:auto;margin-right:auto;" class="tbl" cellspacing="0" cellpadding="0" >
<tr>
<th>Short Name</th>
<th>Total cells</th>
<th>Method</th>
<th>h5ad</th>
<th>Summary</th>
<th>Article/preprint</th>
<th> Original Data</th>
<th> Notes</th>
</tr>

<tr>
<td>Taylor 2020</td>
<td> 100,955 </td>
<td> 10x v2/v3</td>
<td> <a href="https://data.caltech.edu/records/1977">  Download at Caltech Data </a> </td>
<td> L4 larvae neurons selected via flow cytometry </td>
<td> <a href="https://doi.org/10.1101/2020.12.15.422897">Molecular topography of an entire nervous system. </a> </td>
<td> <a href="https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE136049">GSE136049</a> </td>
<td> <a href="https://cengen.org">CeNGEN website </a> 
<a href="http://cengen.shinyapps.io/SCeNGEA"> Shiny R app to explore the data </a>
</td>
</tr>


<tr>
<td>Ben-David 2021</td>
<td> 55,508 </td>
<td> 10x v2</td>
<td> <a href="https://data.caltech.edu/records/1972">  Download at Caltech Data </a> </td>
<td> L2 larvae</td>
<td> <a href="https://doi.org/10.1101/2020.08.23.263798">Whole-organism mapping of the genetics of gene expression at cellular resolution </a> biorxiv 2020.</td>
<td> <a href="https://www.ncbi.nlm.nih.gov/bioproject/PRJNA658829/">PRJNA658829 </a> </td>
<td> Gene count matrix was kindly provided by the authors on request
</td>
</tr>


<tr>
<td>Packer 2019</td>
<td> 89,701 </td>
<td> 10x v2</td>
<td> <a href="https://data.caltech.edu/records/1945"> Download at Caltech Data </a> </td>
<td> Several timepoints of embryo development</td>
<td> <a href="https://science.sciencemag.org/content/365/6459/eaax1971.long">A lineage-resolved molecular atlas of C. elegans embryogenesis at single-cell resolution </a> Science 2019.</td>
<td> <a href="https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE126954">GSE126954</a> </td>
<td> <a href="https://cello.shinyapps.io/celegans/">VisCello app for data exploration </a> 
</td>
</tr>

<tr>
<td>Cao 2017</td>
<td> 35,987 </td>
<td> sci-RNA-seq</td>
<td> <a href="https://github.com/Munfred/wormcells-site/releases/download/cao2017/cao2017.h5ad">  Download <br> 136MB </a> </td>
<td> L2 larvae</td>
<td> <a href="https://doi.org/10.1126/science.aam8940">A lineage-resolved molecular atlas of C. elegans embryogenesis at single-cell resolution </a> Science 2019.</td>
<td> <a href="https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE98561">GSE98561 </a> and <a href="https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSM4318946">GSM4318946 (reprocessed)</a>  </td>
<td> GSM4318946 release was a reannotation of the data 
</td>
</tr>


<tr>
<td>Tintori 2016</td>
<td> 216 </td>
<td>SMARTer kit</td>
<td> - </td>
<td> Embryo through the 16-cell stage</td>
<td> <a href="https://doi.org/10.1016/j.devcel.2016.07.025">A Transcriptional Lineage of the Early C. elegans Embryo </a> Dev Cell 2016.</td>
<td> <a href="https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE77944">GSE77944 </a> </td>
<td> They made a custom visualizer at  <a href="http://tintori.bio.unc.edu/">tintori.bio.unc.edu</a>. 
</td>
</tr>


<tr>
<td>Hashimshony 2012</td>
<td> 96 </td>
<td> CEL-Seq</td>
<td> - </td>
<td>Blastomere cells</td>
<td> <a href="https://doi.org/10.1016/j.celrep.2012.08.003">CEL-Seq: single-cell RNA-Seq by multiplexed linear amplification </a> Cell Rep. 2012</td>
<td> <a href="https://www.ncbi.nlm.nih.gov/sra/?term=SRP014672">SRP014672 </a>  </td>
<td> This was one of the pioneering works in scRNAseq and introduced the CEL-Seq technique.  
</td>
</tr>
</table>
</font>


