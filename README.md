# WormBase single cell tools

This page is a showcase of ideas for single cell tools for WormBase (and eventually the [Alliance](https://www.alliancegenome.org/)) that we are working on.

Please note that everything is in different stages of development and some of the tools may be clunky.

But please, try these tools out, we want your feedbackon how to improve them! You can write to Eduardo at eduardo@wormbase.org or submit a ticket at for the [wormbase helpdesk](https://wormbase.org/tools/support)(select "feature request" as the topic).





# Tools currently being developed
The current plans for WormBase single cell tools were discussed in a seminar by Eduardo da Veiga Beltrame on March 22. [Link to slides](https://docs.google.com/presentation/d/1wQyG6Ww75HRPizOojnGb6N5rx4RH8_DIZXiX83ddD4w/edit?usp=sharing)

## scdefg: Interactive differential expression 

The *scdefg* app consists of a single web page initially displaying a short description, a text input of genes to be highlighted in the output volcano plot, and two lists of cell types to be selected. After submission results are displayed in the form of an interactive volcano plot displaying gene descriptions and two sortable tabular views of the p-values and log fold changes of expression levels showing enriched and depleted genes. The tabular results can be exported to csv and Excel format, or copied to the clipboard.

The app takes a pre-trained scVI mode as input. Training an scVI model is usually quick on GPUs and needs to be done only once, while the differential expression analysis performed on the fly by *scdefg* is less computationally intensive and does not necessarily require GPUs. At WormBase, we have deployed the app on an AWS t2.large cloud instance with only 8GB RAM and 2 vCPU. This basic configuration is sufficient for handling a few concurrent users with results being returned within 15s. 
 
 **Status:** Late stage of development, soon to be available on pip and preprint released

**Repository:** [https://github.com/Munfred/scdefg](https://github.com/Munfred/scdefg)

**Deployments:**  
- [cengen-de](https://www.cengen-de.textpressolab.com/): Deploy with the CeNGEN dataset  

**Current plans:**  
- Add menu for selecting cells across multiple distinct conditions
- Release on pip 
- Integrate CeNGEN and Packer 2019 data

## Gene abundance histograms 
Visualize gene abundances stratified by cell type and experiment

**Status:**  

**Repository:**   

**Deployments:**  

**Current plans:** 

## Heatmaps & dot plots  
Visualize mean gene expression across select genes & cell types

**Status:**

**Repository:** 

**Deployments:**

**Current plans:**

## Swarm plots 
Visualize mean gene expression for many genes in every cell type

**Status:**  

**Repository:**  

**Deployments:**  

**Current plans:**  



# How WormBase processes single cell RNA data: scvi-tools

There are currently hundreds of software tools and pipelines developed for scRNAseq data (see: [https://www.scrna-tools.org](scrna-tools.org)). For processing single sell data at WormBase we have chosen to use the[https://scvi-tools.org](scvi-tools.org) framework. scvi-tools is different from most other scRNAseq tools in that it uses [variational autoencoders](https://arxiv.org/abs/1906.02691) to learn the distribution underlying the input data and create a generative model.  Interested readers can learn more in about the framework in the scvi-tools [documentation](https://docs.scvi-tools.org/en/stable/). Here we briefly highlight a few considerations that lead to our choice of using the the framework for driving scRNAseq analysis.

- Scalability: Using a GPU, scvi-tools can scale to datasets with millions of cells. These large models can be trained in about an hour.

- Consistent Development and Contributors: The scvi-tools codebase [https://github.com/YosefLab/scvi-tools](github.com/YosefLab/scvi-tools) was first introduced in 2017, and published in 2018 by [Lopez et al](https://doi.org/10.1038/s41592-018-0229-2). It currently boasts [35](https://github.com/YosefLab/scvi-tools/graphs/contributors) unique contributors and [52](https://github.com/YosefLab/scvi-tools/releases) releases. 

- Extensible Framework for Analysis: Because the generative model of the data (formally, a hierarchical bayesian network) can be modified to reflect our assumptions about underlying processes, the framework can be extended to model other aspects of scRNAseq data. Currently, extensions include cell type classification and label transfer across batches, modelling single cell protein measurements, performing gene imputation in spatial data, and using a linear decoder to allow for interpretation of the learned latent space. Several peer reviewed articles have been published describing these extensions (see [https://scvi-tools.org/press](scvi-tools.org/press)).

# WormBase deployment philosophy for single cell tools

At the moment, the majority of scRNAseq data is generated using the 10X Genomics Chromium technology, with v2 and v3 chemistry (see [Svensson 2020: A curated database reveals trends in single-cell transcriptomics ](https://doi.org/10.1093/database/baaa073). This is also true for C. elegans scRNAseq data. At least for the time being WormBase will focus development efforts on scRNAseq tools to 10X Genomics data. Two considerations drive this:

i) Data integration of different batches with scvi-tools is more robust when there is more data, and the technology and biological system from the different batches often the same or in any case similar. Attempting to integrate a small number of cells from niche technologies and unique biological systems. For example, the Hashimony 2012 and Tintori 2016 datasets listed below. Niche technologies or small numbers of cells require a case by case treatment, and wrangling the data to h5ad format was not done, as attempting integration with these datasets could lead to a situation where it is impossible to discern biological diffenrences from technical artifacts.

ii) The 10X Genomics data has a widely used, validated and commercially supported data pre-processing workflow, from FASTQ files to gene count matrices. As such, if WormBase decides to uniformly reprocess the FASTQ files in the future, it is ideal that the data pre-processing pipeline is the same for all datasets.


# List of C. elegans single cell datasets

Here we provide a handy collection of all publicly available C. elegans single cell and single nucleus RNA sequencing data. In addition to listing studies and the original data sources, for convenience the data has been repackaged and a direct download link to the data in [.h5ad](https://anndata.readthedocs.io/en/latest/) format is provided. The GitHub repository of this website is [https://github.com/WormBase/single-cell](https://github.com/WormBase/single-cell), and the .h5ad files are hosted as releases [here](https://github.com/Munfred/wormcells-data/releases). If you see a
dataset missing or something incorrect or out of date, please submit an [issue on GitHub](https://github.com/WormBase/single-cell/issues).




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
<td> <a href="https://github.com/Munfred/wormcells-site/releases/download/taylor2020/taylor2020.h5ad">  Download <br> 364MB </a> </td>
<td> L4 larvae neurons selected via flow cytometry </td>
<td> <a href="https://doi.org/10.1101/2020.12.15.422897">Molecular topography of an entire nervous system. </a> </td>
<td> <a href="https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE136049">GSE136049</a> </td>
<td> <a href="https://cengen.org">CeNGEN website </a> 
<a href="http://cengen.shinyapps.io/SCeNGEA"> Shiny R app to explore the data </a>
</td>
</tr>


<tr>
<td>Ben-David 2020</td>
<td> 55,508 </td>
<td> 10x v2</td>
<td> <a href="https://github.com/Munfred/wormcells-site/releases/download/bendavid2020/bendavid2020.h5ad">  Download <br> 145MB </a> </td>
<td> L2 larvae</td>
<td> <a href="https://doi.org/10.1101/2020.08.23.263798">Whole-organism mapping of the genetics of gene expression at cellular resolution </a> biorxiv 2020.</td>
<td> - </td>
<td> Gene count matrix was kindly provided by the authors on request
</td>
</tr>

<tr>
<td>Taylor 2019</td>
<td> 65,450 </td>
<td> 10x v2/v3</td>
<td> <a href="https://github.com/Munfred/wormcells-site/releases/download/taylor2019/taylor2019.h5ad">  Download <br> 217MB </a> </td>
<td> L4 larvae neurons selected via flow cytometry </td>
<td> <a href="https://doi.org/10.1101/737577">Expression profiling of the mature C. elegans nervous system by single-cell RNA-Sequencing </a> biorxiv 2019.</td>
<td> <a href="https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE136049">GSE136049</a> </td>
<td> <a href="https://cengen.org">CeNGEN website </a> 
<a href="http://cengen.shinyapps.io/SCeNGEA"> Shiny R app to explore the data </a>
</td>
</tr>


<tr>
<td>Packer 2019</td>
<td> 89,701 </td>
<td> 10x v2</td>
<td> <a href="https://github.com/Munfred/wormcells-site/releases/download/packer2019/packer2019.h5ad"> Download <br> 653MB </a> </td>
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
<td> <a href="https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE77944">GSE77944 </a> and <a href="https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSM4318946">GSM4318946 (reprocessed)</a>  </td>
<td> They made a custom visualizer at  <a href="http://tintori.bio.unc.edu/">tintori.bio.unc.edu</a>. 
</td>
</tr>


<tr>
<td>Hashimshony 2012</td>
<td> 96 </td>
<td> CEL-Seq</td>
<td> - </td>
<td>embryonic  cells</td>
<td> <a href="https://doi.org/10.1016/j.celrep.2012.08.003">CEL-Seq: single-cell RNA-Seq by multiplexed linear amplification </a> Cell Rep. 2012</td>
<td> <a href="https://www.ncbi.nlm.nih.gov/sra/?term=SRP014672">SRP014672 </a>  </td>
<td> This was one of the pioneering works in scRNAseq and introduced the CEL-Seq technique.  
</td>
</tr>
</table>
</font>


