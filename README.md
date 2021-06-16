# WormBase single cell tools

WormBase has developed two tools for exploring published _C. elegans_ single cell RNA sequencing (scRNAseq) data: `scdefg` for interactive differential expression and `wormcells-viz` for visualization of gene expression. These tools have been deployed at WormBase with public _C. elegans_ datasets and will continue to be updated as new datasets are published. Source code is available at [github.com/WormBase/scdefg](https://github.com/WormBase/scdefg/) and [github.com/WormBase/wormcells-viz](https://github.com/WormBase/wormcells-viz), together with instructions on how to deploy these tools with any scRNAseq dataset.

For a detailed overview, a 45 min talk given on June 3, 2021 explains the tools and current outlook [talk video](https://youtu.be/AGJqm_EIqA8)[slides](https://docs.google.com/presentation/d/1Kv4gPsm-wT8wH_5nAd9sFBFlI6dTvZ8O407Tnj3Gv0Q/edit?usp=sharing).

A list of the _C. elegans_ scRNAseq data made available with these tools is at the end of this page. 

## scdefg: Interactive differential expression 

**Deployment with public _C. elegans_ data**: _(coming very soon)_

The [`scdefg`](https://github.com/WormBase/scdefg) app is written in Python using Flask, and provides a single web page with an interface for selecting two groups of cells according to the existing annotations in the data. For example, the user can select a group according to a combination of cell type, sample, tissue and experimental group. Results are displayed in the form of an interactive volcano plot (log fold change vs p-value) and MA plot (log fold change vs mean expression) that display gene descriptions upon mouseover, and two sortable tabular views of the p-values and log fold changes of expression levels showing enriched and depleted genes. The tabular results can be downloaded in csv and Excel format or copied to the clipboard. The app can be launched from the command line by specifying the path to a trained scVI model and the user may specify data annotations by which the groups may be stratified (e.g. cell type, experiment). Differential expression is performed on the fly and can be done  in reasonable time without using GPUs. We have deployed the app on a cloud instance with only 8GB RAM and 2 vCPUs and observed this configuration is sufficient for handling a few concurrent users with results being returned in about 15s. 



## wormcells-viz: Visualization of gene expression rates

### Deployments 
- **CeNGEN L4 neuron data**: _(coming very soon)_
- **Packer 2019 embryogenesis data**: _(coming very soon)_
- **Ben-David 2021 L2 larvae data**: _(coming very soon)_

The `wormcells-viz` app is written in Javascript and Python and uses [React.js](https://reactjs.org/}) and [D3.js](https://d3js.org) for providing interactive and responsive visualizations of heatmaps, gene expression histograms and swarm plots (see below). Deploying the app requires having the pre-computed gene expression values stored in three custom anndata files as described in the the [wormcells-viz repository](https://github.com/WormBase/wormcells-viz). The following visualizations are currently implemented. 

### Heatmap

Visualization of scVI inferred expression rates for a selection of cell types and genes. The expression rates can be shown as either a traditional heatmap, or as a monochrome dotplot.

### Gene expression histogram

Histograms of the scVI inferred expression rates for a given gene across all cell types in the data. The histogram bin counts are computed from the scVI inferred expression rates for each cell. 


### Swarm plot

For a given cell type, swarm plots visualize the relative expression of a set of genes across all cells annotated in a dataset. These plots are useful for identifying candidate marker genes. 

The Y axis displays the set of selected genes, and the X axis displays the log fold change in gene expression between the cell type of interest and all other cell types. This is computed by doing pairwise differential expression of each annotated cell type vs the cell type of interest. 

-  Y axis: a set of selected genes, evenly spaced
-  X axis: the log fold change of expression of that gene on all cell types, relative to the cell of interest. 
-  0 = baseline expression on reference cell type, 
-  below 0 means lower expression in that cell type relative to reference
-  above 0 means higher expression in cell type relative to reference

A Colab tutorial on how to make swarm plots is available [here](https://colab.research.google.com/github/Munfred/worm-markers/blob/master/2021_06_01_example_swarmplot_Finding_C_elegans_neuron_markers_with_the_CeNGEN_dataset.ipynb) 

# How WormBase processes single cell RNA data: scvi-tools

There are currently hundreds of software tools and pipelines developed for scRNAseq data (see [https://www.scrna-tools.org](scrna-tools.org)). For processing single cell data at WormBase we have chosen to use the [scvi-tools.org](https://scvi-tools.org) framework. scvi-tools is different from most other scRNAseq tools in that it uses [variational autoencoders](https://arxiv.org/abs/1906.02691) to learn the distribution underlying the input data and create a generative model.  Interested readers can learn more in about the framework in the scvi-tools [documentation](https://docs.scvi-tools.org/en/stable/). Here we briefly highlight a few considerations that lead to our choice of using the the framework for driving scRNAseq analysis.

- **Scalability:** Using a GPU, scvi-tools can scale to datasets with millions of cells. These large models can be trained in about an hour.

- **Consistent Development and Contributors:** The scvi-tools codebase [https://github.com/YosefLab/scvi-tools](github.com/YosefLab/scvi-tools) was first introduced in 2017, and published in 2018 by [Lopez et al](https://doi.org/10.1038/s41592-018-0229-2). It currently boasts [35](https://github.com/YosefLab/scvi-tools/graphs/contributors) unique contributors and [52](https://github.com/YosefLab/scvi-tools/releases) releases. 

- **Extensible Framework for Analysis:** Because the generative model of the data (formally, a hierarchical bayesian network) can be modified to reflect our assumptions about underlying processes, the framework can be extended to model other aspects of scRNAseq data. Currently, extensions include cell type classification and label transfer across batches, modelling single cell protein measurements, performing gene imputation in spatial data, and using a linear decoder to allow for interpretation of the learned latent space. Several peer reviewed articles have been published describing these extensions (see [https://scvi-tools.org/press](scvi-tools.org/press)).

# WormBase deployment philosophy for single cell tools

At the moment, the majority of scRNAseq data is generated using the 10X Genomics Chromium technology, with v2 and v3 chemistry. This is also true for _C. elegans_ scRNAseq data. For the time being WormBase will focus development efforts on scRNAseq tools on 10X Genomics data. Two considerations drive this:

- Data integration of different batches with scvi-tools is more robust when there is more data, and when the technology and biological system of each batch is the same or similar. Attempting to integrate a small number of cells from unique technologies and unique biological systems can make it impossible to discern biological differences from technical artifacts.

- The 10X Genomics data has a widely used, validated and commercially supported data pre-processing workflow, from FASTQ files to gene count matrices. This can enable WormBase to uniformly reprocess the FASTQ files in a single pipeline in the future.




# List of C. elegans single cell datasets

Here we provide a curated collection of all C. elegans single cell RNA seq high throughput data wrangled into the [anndata](https://anndata.readthedocs.io/en/stable/) format in `.h5ad` files with standard fields, plus any number of optional fields that vary depending on the metadata the authors provide. We attempt to keep the field names lower case, short, descriptive, and only using valid Python variable names so they may be accessed via the syntax `adata.var.field_name` . The WormBase anndata wrangling convention is described at [github.com/WormBase/anndata-wrangling](https://github.com/WormBase/anndata-wrangling)

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


