# slurm_pipeline
Created for the UofA Med PHX Dep of Transl Neuro Sci pbc-hpc cluster

# nf-core/rnaseq

- [slurm\_pipeline](#slurm_pipeline)
- [nf-core/rnaseq](#nf-corernaseq)
  - [References:](#references)
  - [Steps](#steps)
    - [Install](#install)
    - [Setup](#setup)
    - [Run](#run)
    - [Output](#output)
    - [Process](#process)



## References:
- [nf-core/rnaseq website](https://nf-co.re/rnaseq/3.18.0/)
- [github](https://github.com/nf-core/rnaseq/tree/3.18.0)
- [fasta](https://ftp.ensembl.org/pub/release-113/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz)
- [gtf](https://ftp.ensembl.org/pub/release-113/gtf/homo_sapiens/Homo_sapiens.GRCh38.113.gtf.gz)
  
## Steps

### Install
- ```git clone https://github.com/nf-core/rnaseq.git```
- Make sure you have nextflow installed (```nextflow info```)
  - [nextflow](https://www.nextflow.io/docs/latest/install.html)
- Go to [LAUNCH](https://nf-co.re/launch/?pipeline=rnaseq&release=3.18.0) on the rnaseq website to create your nf-params.json file

### Setup
- Create your sheet.csv according to [Full samplesheet instructions](https://nf-co.re/rnaseq/3.18.0/docs/usage/#:~:text=for%20your%20samples.-,Full%20samplesheet,-The%20pipeline%20will)
  - Includes
    - ids
    - paths to all fastq files (R1 & R2)
    - strandedness
    - The last columns can be custom (first 4 must be ```sample,fastq_1,fastq_2,strandedness```)
- Have an out dir
- fasta & gtf path
- kraken_db folder (see [k2](https://benlangmead.github.io/aws-indexes/k2))
  - [Standard](https://genome-idx.s3.amazonaws.com/kraken/k2_standard_20241228.tar.gz)
- Customize other options to what you prefer
- Set **profile** to what container you will be using.
  - Have your container loaded with ```module load "container"``` (such as singularity or apptainer)
- Take your generated ```nf-params.json``` file and add it to the rnaseq

### Run
```bash
nextflow run path/to/rnaseq -name run1 -profile apptainer -params-file path/to/rnaseq/nf-params.json
```

### Output
Check out dir


### Process
Being able to use SLURM is crucial and will need to edit the ```nextflow.config```
Add ```executor = 'slurm'``` to the process block.
```java
// Default publishing logic for pipeline
process {
    executor = 'slurm'
    publishDir = [
        path: { "${params.outdir}/${task.process.tokenize(':')[-1].tokenize('_')[0].toLowerCase()}" },
        mode: params.publish_dir_mode,
        saveAs: { filename -> filename.equals('versions.yml') ? null : filename }
    ]
}
```
Note:
By default the config file is set up to request the correct amount of resources for every sbatch. And will retry with more if needed. There is no need to specify resources allocation in the config file.