# D.zibethinus_WGS
Replication of WGS analysis of Durian, from article by Teh et. al., 2017 (doi:10.1038/ng.3972). Course project in Genome Analysis 2023, Uppsala University.

For Project plan and aim, see Wiki

For project notebook with recorded progress, use the following link: https://colab.research.google.com/drive/1f7bKYuTVSuLHd1ELmxQJjbu3FGZvmAFK?usp=sharing (NB: the notebook is originally for private use and not edited for reading)

The following document describes the detailed workflow and results of the analyses.

## Workflow

![Untitled Diagram (2)](https://github.com/Cleiti/D.zibethinus_WGS/assets/52427029/0ad68d07-3566-461c-b2d4-935b4b898c95)

### Raw data

This project used three kinds of input raw data: genomic short Illumina reads and genomic long PacBio reads, as well as transcriptomic short Illumina reads. All the input files were in **.fq.gz** format

The given input data belonged to three different scaffolds (6, 10 and 11). All analyses up to feature counting are performed for all three of these scaffolds, using either separate code files or one for all. For the last two steps of the analysis, due to lack of time, only results from scaffold 10 are used.

The reads represent nine different combinations cultivar and plant organ, corresponding to a specific experiment ID. These are presented below:

Experiment ID cultivar  plant organ
SRR6040092  Musang King leaf
SRR6040093  Musang King root
SRR6040094  Musang King aril
SRR6040095  Musang King aril
SRR6040096  Musang King stem
SRR6040097  Musang King aril
SRR6156066  Monthong  aril
SRR6156067  Monthong  aril
SRR6156069  Monthong  aril

### Short reads preprocessing

Raw illumina reads, both genomic and transcriptomic, were Quality Controlled, trimmed, and Quality Controlled again. The programs used were **FastQC** and **Trimmomatic**. The code for these jobs can be found in folders "DNA_prep_illumina" and "RNA_prep". Transcriptome reads from only one experiment (SRR6040095) needed trimming, and the rest were left unchanged.

The genomic reads passed QC well, while transcriptomic reads did not (both before and after trimming), failing among others in metrics of overrepresenteed sequences and per sequence GC content. The analysis was nevertheless continued with the same data.

### Initial assembly

The initial assembly was made using long genomic PacBio reads. No initial preprocessing was made, because the assembly software used - **Canu** - includes a QC. The command outputed a **.fasta** file with an assembly. The code for this job can be found in folder "DNA_prep_assembly_pacbio".

### Assembly improvement

The assembly based on genomic long reads was further improved using genomic short reads. The reads were first indexed and aligned to the assembly using software **BWA** with options **index** and **mem**, creating an alignment file in **.sam** format. The alignment was converted to a compressed format **.bam**, as well as sorted by position and indexed, using **SAMtools**.

The initial assembly was subsequently corrected with the alignment using software **Pilon**. The final assembly was Quality Controlled using **QUAST** and the sequence repeats were softmasked using **RepeatMasker**.

The code for these jobs can be found in folder "DNA_post_assembly_pacbio".

### Genome annotation

The transcriptome reads were aligned (splice-aware) to the final genome assembly using **STAR**, and the alignment file was compressed, sorted and indexed using **SAMtools** as in the previous step. Next, the final genome was annotated using the alignment files, with software **BRAKER**. This produced feature files in **.gtf** and **.gff** formats. The code for these jobs can be found in folder "RNA_alignment" and "DNA_annotation". 

### Feature counting and differential expression analysis

Genomic features were counted from the **.gtf** file using **HTseq**, and the resulting count file was used for expression comparison between the different cultivars and plant organs using R package **DEseq2**. 


