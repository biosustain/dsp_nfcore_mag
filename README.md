# Documentation for running nf-core/MAG within DTU-Biosustain
Repository for documentation, tips and tricks, best practices for running [nf-core/MAG](https://nf-co.re/mag/5.2.0/) v.5.2.0 within [DTU-Biosustain](https://www.biosustain.dtu.dk/) infrastructure.

## Notes on parameter choices and execution

This section documents parameter changes, computational considerations, and practical tips based on running `nf-core/mag` on the DTU Biosustain infrastructure using **Seqera Platform**.

### Modified parameters

During execution, several parameters were adjusted from the default settings to better suit the dataset characteristics and available resources. These included:

- **Assembler and binning sensitivity**
  - More sensitive assembly and binning settings were used to improve recovery of low-abundance genomes.
  - This increases runtime and memory usage, particularly during assembly and binning steps.

- **Mapping sensitivity**
  - More sensitive read-mapping settings were selected to improve coverage estimation and downstream abundance calculations.
  - This has a noticeable impact on CPU time during mapping steps.

- **Bin size thresholds**
  - Maximum and minimum bin size thresholds were adjusted to better reflect the expected genome sizes in the dataset and to reduce spurious bins.

All parameter changes were passed explicitly via the `params` configuration in Seqera to ensure full reproducibility.

---

## Computationally intensive steps

Some processes (while having lots of data) in `nf-core/mag` are significantly more computationally demanding than others and should be scheduled on nodes with higher memory and CPU availability:

- **Assembly (MEGAHIT / SPAdes)**
  - High memory usage, especially for co-assemblies
  - Runtime scales strongly with sequencing depth and sample number

- **Binning (MetaBAT2, MaxBin2, CONCOCT)**
  - CPU- and memory-intensive
  - Running multiple binning tools in parallel substantially increases resource demands


For these steps, it is recommended to use nodes with increased RAM and multiple CPUs. **NOTE** : If paused or failed, the pipeline can not be restarted using a different machine.

---

## Running the pipeline with Seqera Platform

The pipeline was executed using **Seqera Platform**, which provides an intuitive interface for managing nf-core workflows.

### Passing custom parameters in Seqera

Custom parameters were supplied via the **“Pipeline parameters”** or **“Advanced parameters”** section in the Seqera web interface:

- Custom Nextflow configuration snippets can be provided if required

All parameters passed through Seqera are recorded with the run, ensuring full traceability and reproducibility.

---

## Metadata file preparation

Correct formatting of metadata files is essential for successful pipeline execution.

**Best practices:**
- Edit metadata files as **plain text** (`.tsv` or `.csv`)
- Avoid spreadsheet software when possible, as it may introduce:
  - Hidden characters
  - Non-breaking spaces
  - Automatic formatting of sample IDs
- Ensure consistent use of delimiters (tabs for `.tsv`)
- Double-check headers and sample names for exact matches with input files

A simple text editor (e.g. `nano`, VS Code) is recommended to prevent formatting issues.

---

## Reproducibility

All runs were executed with:
- Explicit parameter definitions
- Versioned nf-core/mag pipeline
- Containerized software environments

This ensures that analyses can be reproduced or extended in future runs.


## MultiQC reports and quality assessment

The `nf-core/mag` pipeline generates comprehensive **MultiQC reports** that summarise quality control and processing statistics across all pipeline stages. These reports provide a high-level overview of data quality, processing performance, and genome recovery success.

Key information available in the MultiQC reports includes:

### Read quality and preprocessing
- **Read counts before and after filtering**
- **Percentage of reads removed** during quality trimming and filtering
- **Read length distributions**
- **Per-base quality scores**
- **Adapter content and overrepresented sequences**

These metrics allow evaluation of sequencing quality and the effectiveness of preprocessing steps such as trimming and filtering.

---

### Read mapping and coverage
- **Mapping rates per sample**
- **Percentage of reads mapped to assemblies**
- **Coverage statistics across contigs and MAGs**

These results indicate how well reads map back to the assembled contigs and recovered genomes, and provide insight into sequencing depth and assembly completeness.

---

### Assembly statistics
- **Total assembly size**
- **Number of contigs**
- **N50 and L50 metrics**
- **Contig length distributions**

Assembly statistics help assess fragmentation and overall assembly quality, and allow comparisons between samples or different parameter settings.

---

### Binning and MAG recovery
- **Number of bins recovered per sample**
- **Completeness and contamination estimates** (via CheckM)
- **Distribution of MAG quality categories** (high-, medium-, low-quality bins)

These summaries are essential for evaluating the success of the binning process and for selecting MAGs for downstream analyses.

---

### Resource usage and pipeline performance
- **CPU and memory usage per process**
- **Runtime per pipeline step**
- **Identification of computational bottlenecks**

These metrics are particularly useful for optimizing future runs and selecting appropriate computational resources.

---

## Interpretation and use

The MultiQC reports serve as a central quality control checkpoint, enabling:
- Rapid identification of problematic samples
- Comparison of parameter choices across runs
- Informed decisions for downstream analyses (e.g. filtering MAGs by quality)

All MultiQC outputs are versioned and stored alongside the pipeline run, ensuring transparency and reproducibility.


## General recommendations
	-	Start with default parameters and modify only when necessary
	-	Monitor resource usage through Seqera to identify heavy steps
	-	Keep parameter changes documented for reproducibility
	-	Validate results using the MultiQC summaries rather than individual tool outputs
	-	Double check the path names of every input file 
  -	Metadata file better to be place on the results folder
	

  
