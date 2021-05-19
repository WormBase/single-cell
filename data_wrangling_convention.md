This document describes how WormBase should wrangle single cell RNA seq data into the [anndata](https://anndata.readthedocs.io/en/stable/) format in `.h5ad` files with standard fields, plus any number of optional fields that vary depending on the metadata the authors provide. 

As possible, we attempt to keep the field names lower case, short, descriptive, and only using valid Python variable names so they may be accessed via the syntax `adata.var.field_name` 

Our goal is to standartize the naming convention for frequently used fields so that code may be reused without headaches changing variable names. The .h5ad file should only contain genes and cells with at least one count.

For now we are trying to keep these guidelines simple so that we can easily stick to them. Going forward we will update them as we learn from experience. For example, at some point WormBase may start uniformly reprocessing the data, so a version field may be needed in the adata.uns

Below we provide a standard description of the mandatory fields we use in all datasets, plus some common optional ones that we have used so far (not all, each dataset may have different metadata provided by the authors). 

### `adata.var`: gene IDs, names and descriptions 
|Field name | Description | Type | Example value | Optionality|
|-----------|-------------|------|-------|-----|
| `adata.var.index` | WormBase gene ID, must be unique | string | `WBGene00010957`| Required|
| `adata.var.gene_id` | WormBase gene ID, repeat values from index | string | `WBGene00010957`|Required
| `adata.var.gene_name` | WormBase gene name | string | `nduo-6 `|Required|
| `adata.var.gene_description` | WormBase short gene description. Full list available for download [here](https://www.alliancegenome.org/downloads) | string | `Predicted to have NADH dehydrogenase (ubiquinone) activity. Predicted to localize to integral component of membrane; mitochondrial membrane; and respirasome.`|Optional|

### `adata.obs`: cell barcode, experiment, batch, original study, cell type
|Field name | Description | Type | Example value | Optionality|
|-----------|-------------|------|-------|-----|
| `adata.var.index` | The batch name joined with cell barcode witha `+` char | string | `F4_1+TGTAACGGTTAGCTAC-1 `| Required|
| `adata.var.study` | A unique shorthand for the study that published the data, ideally in the style <first author><year> all lower case. The .h5ad file should have the same name as the study it corresponds to.  | categorical | `taylor2020`| Required|
| `adata.obs.sample_batch` | The run that produced the corresponding barcode. Most of the time batch and experiment will be the same, but with multiplexing sometimes an one batch can have multiple experiments | categorical | `F4_1`|Required|
| `adata.obs.sample` | The biological sample that is in this batch | categorical string | `L2 larvae fourth repeat`|Required|
| `adata.obs.sample_description` | Description of the sample. This is mandatory because otherwise it will be very easy to confuse two samples from their name without carefully reading the paper or contacting authors | categorical string | `F4_1`|Required|
| `adata.obs.barcode` | The cell barcode | string | `AAACCCAAGATCGCTT-1`|Required|
| `adata.obs.cell_type` | The cell type annotation provided by the authors. Should be `not provided` if not available | categorical | `ASJ`|Required|
| `adata.obs.cell_subtype` | The cell subtype annotation if provided by the authors | categorical | `BWM_head_row_1`|Optional|
| `adata.obs.tissue` | The tissue annotation if provided by the authors | categorical | `Intestine`|Optional|
