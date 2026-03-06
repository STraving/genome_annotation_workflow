# genome_annotation_workflow
Guideline for genome assembly of bacteria isolated from environmental material. We assume no taxonomy before starting.
The workflow currently includes:
1. Quality check and trimming
2. Assembly
3. Taxonomy
4. Gene annotation

Specific for Ucloud:

- Spades will require a minimum amount of RAM, run it on a job with 32-64 GB RAM.

- GTDB-Tk will also require a number of CPUs, run it on a job with atleast 4 CPUs.

- In the HADAL center we save raw data in its own separate directory. Please keep your intermediate files in your own work directory.

-Due to multiple people working in the same workspace on Ucloud many of the tools are installed separately. Just activate the right conda environment for the tool you need.

Conda environments:

To check which tools we have write:

`conda-env list`

to activate:

`conda activate env-name`

to deactivate environment:

`conda deactivate`

1. Quality check

   `conda activate fastq`

   `fastp -i forward_reads.fastq.gz -I reverse_reads.fastq.gz -o output_forward.fq -O output_reverse.fq`

check the stats after the run, example:


   `conda deactivate`

2. Genome assembly using Spades

 To learn more about Spades see https://ablab.github.io/spades/
   
   We run the assembly with the --isolate function.

   `conda activate spades`
   
   `spades.py --isolate -1 R1.fastq.gz -2 R2.fastq.gz -o spades_out -m 24 -t 4`

   `conda deactivate`

   Here the example is shown running on a job with 24GB RAM and 4 CPUs, for assembling this environmental isolate with ~50x coverage       from sediment, it worked fine.
   
  2.b   filtering of contigs
  
  If your assembly contains alot of smaller contigs ≥ 1000 bp it is probably helpful to filter these out. you can remove them with        seqKit.
  
  `seqkit seq -L 1000 contigs.fasta > contigs_1kb.fasta`

   You can adjust the cutoff size if you need it to be more lenient or strict. 
  
4. Taxonomic assignment using GTDB-Tk
   To learn more about GTDB-Tk see https://github.com/Ecogenomics/GTDBTk

   
