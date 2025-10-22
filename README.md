# **GenomeBoost** - Super-fast Genome Analysis Software for Sequencing Data

## Introduction

**GenomeBoost** is a high-speed genome analysis software that produces results identical 
to those from the **GATK** Best Practices pipeline, while achieving over 10× faster 
performance on most CPU-based servers — without requiring additional hardware such as GPUs or FPGAs.
**GenomeBoost** is the core software of **[GenomeBoost Server™](https://genomeboostserver.github.io)**.

**GenomeBoost** is optimized based on the *BWA* implementation under the Apache License 
and integrates key functionalities of **GATK**, including MarkIlluminaAdapters, 
MergeBamAlignment, MarkDuplicates, BaseRecalibrator, and ApplyBQSR. 
The current version of **GenomeBoost** supports short-read data ranging from 75 bp to 300 bp.

**GenomeBoost** is proprietary software developed by Genome4me, Inc.  
Please refer to the LICENSE file for details on the licensing policy.  
The **GenomeBoost** BWA module is based on the *BWA* source code under the Apache License.

## How to execute **GenomeBoost**

1. Index the reference FASTA file.
```
$ genomeboost index <reference fasta>
```

2. Execute **GenomeBoost** to produce an analysis-ready SAM/BAM file.
```
$ genomeboost preprocess --mark_illumina_adapter --fix_alignment --mark_dup \
  --bqsr <reference fasta> <read1.fastq> [<read2.fastq>]
```

- The `--mark_illumina_adapter` option corresponds to the **GATK MarkIlluminaAdapters** module.  
- The `--fix_alignment` option corresponds to the **GATK MergeBamAlignment** module.  
- The `--mark_dup` option corresponds to the **GATK MarkDuplicatesSpark** module.  
- The `--bqsr` option corresponds to the **GATK BaseRecalibrator** and **ApplyBQSR** modules.  

The **`preprocess`** command automatically configures the number of threads and 
memory usage based on the available system resources.  
Users can override these defaults using the `--threads` option to set the number of threads, 
and can control memory usage with either the `--high-memory` or `--low-memory` options.  
The `--high-memory` option allows **GenomeBoost** to use more memory for faster performance 
(at the risk of running out of memory), while the `--low-memory` option limits memory usage 
to reduce the risk of out-of-memory errors.

The **`preprocess`** command also accepts standard *BWA-MEM* options such as `-Y` or `-K`.  
For a complete list of available commands and options, run:
```
$ genomeboost --help
$ genomeboost preprocess --help
```

For input FASTQ files larger than 30x human WGS data, **GenomeBoost** may need
larger memory than 64 GB. Because **GenomeBoost** leverages most of the system
memory to execute faster, only a single **GenomeBoost** process should be executed
at a time.

Please make sure that there is enough free disk space (at least 4× the size of input
FASTQ files) because **GenomeBoost** stores intermediate data into temporary files.

# **GATK** compatible output

By examining >430 human WGS datasets, we confirmed that **GenomeBoost** produces
SAM output, mark-illumina-adapter metrics, mark-duplicate metrics, and
BQSR report files identical to those of the **GATK** best-practice pipeline.  
In our test, we used the WARP (WDL Analysis Research Pipelines) implementation
of the **GATK** best-practice and the following software versions:
- WARP WholeGenomeGermlineSingleSample v3.3.4
- GATK 4.6
- Picard 3.2.0
- BWA 0.7.18

## Detailed **GATK** steps

Here are the detailed steps of the **GATK** pipeline we tested:
- FastqToSam
- MarkIlluminaAdapters
- SamToFastq
- BWA
- MergeBamAlignment
- MarkDuplicatesSpark
- BaseRecalibrator
- ApplyBQSR

The **GATK** WARP pipeline executes BaseRecalibrator and ApplyBQSR steps in multiple
processes by dividing the whole genome into tens of intervals to run faster.
However, the output results of the multi-process BaseRecalibrator are
slightly different from the output files of a single-process BaseRecalibrator
because of random numbers generated in the middle of the process.  
To remove the random fluctuations in our test, we executed the **GATK** BaseRecalibrator and
ApplyBQSR in a single process and confirmed that the output files of **GenomeBoost**
are identical to those of the **GATK** BaseRecalibrator and ApplyBQSR.

# Support

If you have any questions about this software, please contact **[support@genome4me.com](mailto:support@genome4me.com)**
