# genome_annotation_workflow
Guideline for genome assembly of bacteria isolated from environmental material. We assume no taxonomy before starting.
The workflow currently includes:
1. Quality check and trimming
2. Assembly
3. Taxonomy
4. Gene annotation

Specific for Ucloud:

-Spades and potentially GTDB-Tk will require some amount of RAM, run it on a job with 32-64 GB RAM.

-We save raw data in its own separate directory. Please keep your intermediate files on your own work directory.

-Due to multiple people working in the same workspace on Ucloud many of the tools are installed separately. Just activate the right conda environment for the tool you need.

1. Quality check

   `conda activate fastq`
   
`fastp -i forward_reads.fastq.gz -I reverse_reads.fastq.gz -o output_forward.fq -O output_reverse.fq`

check the stats after the run, example:


3. Genome assembly using Spades
   We run the assembly with the --isolate function.
   
`spades.py --isolate -1 R1.fastq.gz -2 R2.fastq.gz -o spades_out`

3. Taxnomic assignment
