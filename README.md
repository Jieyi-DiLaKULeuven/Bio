# REDENToR 
_(**R**etrotransposable **E**lements’ **D**ifferential **E**xpression by de **N**ovo **T**ranscript**o**me **R**econstruction)_

**A bioinformatics pipeline to quantify retrotransposon-associated transcriptions (RATs).**

This pipeline was coded in Julia language. Users need to map the RNA-seq data first (hg38 or mm10, using STAR is recommended). In REDENToR, transcriptomes were subsequently assembled using StringTie, under guidance of the transcript annotation (Ensembl is recommended). All de novo assembled transcription annotations from the given samples were merged using “StringTie --merge”. HTSeq-counts were used to count the read numbers of known and novel genes. Non-coding transcripts (transcripts not overlapping annotated coding genes) in the merged transcription annotations were assigned as RATs when any of its exons overlapped with a retrotransposon repeat annotation (LTR, LINE, or SINE, based on RepeatMasker annotation from UCSC). If a transcript overlapped with >1 annotated repeat, the retrotransposon with the highest overlap was assigned to this RAT.

## Dependencies
### Software
Below softwares are required:
- [Julia](https://julialang.org/) v1.2.0 above
- [StringTie](https://ccb.jhu.edu/software/stringtie/)
- [HTSeq](https://htseq.readthedocs.io/en/release_0.11.1/)

Below Julia package is required:
- [SortingAlgorithms.jl](https://github.com/JuliaCollections/SortingAlgorithms.jl)

### Data
- Mapped bam file(s), sorted by coordinates;
- ENSEMBL GTF file.

## Usage
`julia `_\[_`-p `_n \]_` redentor.jl `_\<parameters\>_

### Input
- `--bam` (or `-b`) _\<file1.bam,file2.bam,...\>_ Input mapped bam file (sorted by coordinate). For multiple files input, seperate them with comma.
- `--id`  (or `-i`) _\<id1,id2,...\>_ ID of each sample. Should be the same number as bam files.
- `--output` (or `-o`) _\<dir\>_ The directory for the output. Should be pre-created.
- `--genome` (or `-g`) `hg38`|`mm10` The sequenced genome type. Only support hg38 and mm10 so far.
- `--annotation` (or `-a`) _\<file.gtf\>_ The transcription annotation file. Tested on Ensembl annotation.
- `--seqstrand` (or `-s`) `first`|`second`|`none`  The sequenced strand. Optional (default="none").
  - `first` : Assumes a stranded library fr-firststrand.
  - `second`: Assumes a stranded library fr-secondstrand.
- `-p` _\<thread_num\>_ Assign thread number. It is a Julia parameter and should be putted between `julia` and `redentor.jl`. Optional (default=1).

### Output
`RAT_info.tsv`, `RAT_readcount.tsv`, and `RAT_RPKM.tsv` are the information, read count, and read per kilobase of transcript, per million mapped reads of all detected RATs, respectively. For the RAT gene structure, please see `work/StringTieMerged.gtf`.

### Tips
- When the pipeline is interupted and rerun in the same folder, the previously finished steps will be skipped.
