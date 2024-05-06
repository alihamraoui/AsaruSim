[![Made with Docker](https://img.shields.io/badge/Made_with-Docker-blue?logo=docker&logoColor=white)](https://www.docker.com/ "Go to Docker homepage")
[![dependency - Nextflow](https://img.shields.io/badge/dependency-Nextflow-green)](https://pkg.go.dev/Nextflow)

[![GitHub tag](https://img.shields.io/github/tag/alihamraoui/AsaruSim?include_prereleases=&sort=semver&color=blue)](https://github.com/alihamraoui/AsaruSim/releases/)
![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)
[![issues - AsaruSim](https://img.shields.io/github/issues/alihamraoui/AsaruSim)](https://github.com/alihamraoui/AsaruSim/issues)
[![Twitter Follow](https://img.shields.io/twitter/follow/Genomique_ENS.svg?style=social)](https://twitter.com/Genomique_ENS)

# Asaru Sim Documentation

`AsaruSim` is an automated Nextflow workflow designed for simulating 10x single-cell Nanopore reads. It allows to benchmark and optimize single-cell Nanopore long read data processing pipelines.

![Workflow Schema](images/workflow.png)

## Prerequisites

Before starting, ensure the following tools are installed and properly set up on your system:

- **Nextflow**: A workflow engine for complex data pipelines. [Installation guide for Nextflow](https://www.nextflow.io/docs/latest/getstarted.html).
- **Docker** or **Singularity**: Containers for packaging necessary software, ensuring reproducibility. [Docker installation guide](https://docs.docker.com/get-docker/), [Singularity installation guide](https://sylabs.io/guides/3.0/user-guide/installation.html).
- **Git**: Required to clone the workflow repository. [Git installation guide](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## Installation

Clone the `AsaruSim` GitHub repository:

```bash
git clone https://github.com/alihamraoui/AsaruSim.git
cd AsaruSim
```

## Configuration

Customize runs by editing the `nextflow.config` file and/or specifying parameters at the command line.

### Pipeline Input Parameters

Here are the primary input parameters for configuring the workflow:

| Parameter          | Description                                                   | Default Value                                 |
|--------------------|---------------------------------------------------------------|-----------------------------------------------|
| `matrix`           | Path to the count matrix csv file (required)                  | `test_data/matrix.csv`                        |
| `bc_counts`        | Path to the barcode count file                                | `test_data/test_bc.csv`                       |
| `transcriptome`    | Path to the reference transcriptome file (required)           | `test_data/transcriptome.fa`                  |
| `features`         | Matrix feature counts                                         | `transcript_id`                               |
| `gtf`              | Path to transcriptom annotation .gtf file                     | `null`                                        |
| `cell_types_annotation`    | Path to cell type annotation .csv file                | `null`                                        |

### Error/Qscore Parameters

Configuration for error model:

| Parameter          | Description                                                   | Default Value                                 |
|--------------------|---------------------------------------------------------------|-----------------------------------------------|
| `trained_model`    | Badread pre-trained error/Qscore model name                   | `nanopore2023`                                |
| `badread_identity` | Comma-separated values for Badread identity parameters        | `"98,2,99"`                                   |
| `error_model`      | Custom error model file (optional)                            | `null`                                        |
| `qscore_model`     | Custom Q-score model file (optional)                          | `null`                                        |
| `build_model`      | to build your own error/Qscor model                           | `false`                                       |
| `model_fastq`      | reference real read (.fastq) to train error model   (optional)      | `false`                                       |
| `ref_genome`       | reference genome .fasta file (optional)                       | `false`                                       |

### Additional Parameters

| Parameter          | Description                                                   | Default Value                                 |
|--------------------|---------------------------------------------------------------|-----------------------------------------------|
| `amp`              | Amplification factor                                          | `1`                                           |
| `outdir`           | Output directory for results                                  | `"results"`                                   |
| `projectName`      | Name of the project                                           | `"test_project"`                              |

### Run Parameters

Configuration for running the workflow:

| Parameter         | Description                        | Default Value             |
|-------------------|------------------------------------|---------------------------|
| `threads`         | Number of threads to use           | `4`                       |
| `container`       | Docker container for the workflow  | `'hamraouii/wf-SLSim'`    |
| `docker.runOptions` | Docker run options to use       | `'-u $(id -u):$(id -g)'`  |

## Execution

To run the workflow with basic example:

You can simulate a realistic disribution of per barcodes UMI counts by providing in addition to the filtered counts matrix a .csv file of BC counts.  AsaruSim will add a random transcrips count to fit the real distribution.
```bash
nextflow run main.nf --matrix test_data/matrix.csv \
                     --bc_counts test_data/test_bc.csv \
                     --transcriptome test_data/transcriptome.fa \

```
User can choose among 4 ways to simulate template reads.
- use a real count matrix
- estimated the parameter from a real count matrix to simulate synthetic count matrix 
- specified by his/her own the input parameter
- a combination of the above options

#### use a real count matrix
```bash
nextflow run main.nf --matrix test_data/matrix.csv \
                     --transcriptome test_data/transcriptome.fa
```

#### estimated the parameter from a real count matrix
```bash
nextflow run main.nf --matrix test_data/matrix.csv \
                     --transcriptome test_data/transcriptome.fa
```

#### specified by his/her own the input parameter
```bash
nextflow run main.nf --matrix test_data/matrix.csv \
                     --transcriptome test_data/transcriptome.fa
```

#### combination case
```bash
nextflow run main.nf --matrix test_data/matrix.csv \
                     --transcriptome test_data/transcriptome.fa
```

We use SPARSIM tools to simulate count matrix. for more information a bout synthetic count matrix, please read [SPARSIM](https://gitlab.com/sysbiobig/sparsim/-/blob/master/vignettes/sparsim.Rmd?ref_type=heads#Sec_Input_parameter_estimated_from_data) documentaion.

## example:
```bash
nextflow run main.nf --matrix test_data/matrix.csv \
                     --bc_counts test_data/test_bc.csv \
                     --transcriptome test_data/transcriptome.fa \

```

## Results

After execution, results will be available in the specified `--outdir`. This includes simulated Nanopore reads `.fastq`, along with log files and QC report.

## Cleaning Up

To clean up temporary files generated by Nextflow:

```bash
nextflow clean -f
```
## Acknowledgements

- We would like to express our gratitude to [Youyupei](https://github.com/youyupei) for the development of [SLSim](https://github.com/youyupei/SLSim), which has been foundational to the `AsaruSim` workflow.
- Additionally, our thanks go to the teams behind [Badread](https://github.com/rrwick/Badread) and [SPARSim](https://gitlab.com/sysbiobig/sparsim), whose tools are integral to the `AsaruSim` workflow.

## Support and Contributions

For support, please open an issue in the repository's "Issues" section. Contributions via Pull Requests are welcome. Follow the contribution guidelines specified in `CONTRIBUTING.md`.

## License

`AsaruSim` is distributed under a specific license. Check the `LICENSE` file in the GitHub repository for details.
