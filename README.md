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

- Due to multiple people working in the same workspace on Ucloud many of the tools are installed separately. Just activate the right conda environment for the tool you need.

Conda environments:

To check which tools we have write:

      conda-env list

to activate:

      conda activate env-name

to deactivate environment:

      conda deactivate

1. Quality check

         conda activate fastq

         fastp -i forward_reads.fastq.gz -I reverse_reads.fastq.gz -o output_forward.fq -O output_reverse.fq

check the stats after the run.

2. Genome assembly using Spades

    To learn more about Spades see https://ablab.github.io/spades/
   
    We run the assembly with the --isolate function.

         conda activate spades
   
         spades.py --isolate -1 R1.fastq.gz -2 R2.fastq.gz -o spades_out -m 24 -t 4

         conda deactivate

    Here the example is shown running on a job with 24GB RAM and 4 CPUs, for assembling this environmental isolate with ~50x coverage from sediment, it took around 25 min and worked without issue.

3. Filtering of contigs:
   
    If your assembly contains alot of smaller contigs ≥ 1000 bp it is probably helpful to filter these out. You     can remove them with seqkt.
  
        seqkt seq -L 1000 contigs.fasta > contigs_1kb.fasta

    You can adjust the cutoff size if you need it to be more lenient or strict. 
    
    Filtered contig file is moved to a new folder where all your genomes can be gathered for taxonomy analysis.
   
5. Taxonomic assignment using GTDB-Tk
   
    To learn more about GTDB-Tk see https://github.com/Ecogenomics/GTDBTk

    Example for a single genome, therefore the --skip_ani_screen option:
   
            gtdbtk classify_wf --genome_dir genomes/ --out_dir GTDB-Tk --cpus 2 --skip_ani_screen --extension fasta --scratch_dir gtdbtk_scratch

7. Gene annotation using Prokka
   
    To learn more about Prokka see https://github.com/tseemann/prokka
   
    Consider cross checking results with other annotation tools.

   
