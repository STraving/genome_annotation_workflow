# genome_annotation_workflow
name: Bioinformatics Pipeline Guide

on:
  workflow_dispatch:
    inputs:
      step:
        description: 'Which pipeline step to document?'
        required: true
        default: 'all'
        type: choice
        options:
          - all
          - fastp
          - spades
          - gtdb-tk
          - prokka

jobs:
  pipeline-documentation:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Display All Commands (QC & Trimming with Fastp)
        if: github.event.inputs.step == 'all' || github.event.inputs.step == 'fastp'
        run: |
          echo "## STEP 1: Quality Control & Trimming with Fastp"
          echo ""
          echo "### Command to run on HPC:"
          echo '```bash'
          echo 'fastp -i input_R1.fastq.gz -I input_R2.fastq.gz \\'
          echo '      -o trimmed_R1.fastq.gz -O trimmed_R2.fastq.gz \\'
          echo '      --detect_adapter_for_pe \\'
          echo '      --cut_front --cut_tail --cut_mean_quality 20 \\'
          echo '      --html fastp_report.html \\'
          echo '      --json fastp_report.json'
          echo '```'
          echo ""
          echo "### What to look for in output:"
          echo "- Check fastp_report.html in your browser"
          echo "- Look for: adapter content, quality scores, GC content"
          echo "- Good reads should have mean quality > 30"
          
      - name: Display Genome Assembly with SPAdes
        if: github.event.inputs.step == 'all' || github.event.inputs.step == 'spades'
        run: |
          echo "## STEP 2: De Novo Genome Assembly with SPAdes"
          echo ""
          echo "### Command to run on HPC:"
          echo '```bash'
          echo 'spades.py --isolate \\'
          echo '         -1 trimmed_R1.fastq.gz \\'
          echo '         -2 trimmed_R2.fastq.gz \\'
          echo '         -o spades_output \\'
          echo '         -t 32 -m 256'
          echo '```'
          echo ""
          echo "### What to look for in output:"
          echo "- Check spades_output/contigs.fasta"
          echo "- Run: seqkit stats contigs.fasta"
          echo "- Good assembly: N50 > 50kb, L50 < 10 for bacterial genomes"
          echo "- Check contigs.fasta file size (should be ~4-10 Mb for bacteria)"
          
      - name: Display Classification with GTDB-Tk
        if: github.event.inputs.step == 'all' || github.event.inputs.step == 'gtdb-tk'
        run: |
          echo "## STEP 3: Taxonomic Classification with GTDB-Tk"
          echo ""
          echo "### Command to run on HPC:"
          echo '```bash'
          echo 'gtdbtk classify_wf --genome_dir . \\'
          echo '                    --out_dir gtdbtk_output \\'
          echo '                    --cpus 32 \\'
          echo '                    --pplacer_cpus 8'
          echo '```'
          echo ""
          echo "### What to look for in output:"
          echo "- Check gtdbtk_output/classify/genome.summary.tsv"
          echo "- Look for: Classification (d__, p__, c__, o__, f__, g__, s__)"
          echo "- Confidence values should be high if > 95%"
          echo "- If novel strain: may show as unclassified at species level (s__)"
          
      - name: Display Gene Annotation with Prokka
        if: github.event.inputs.step == 'all' || github.event.inputs.step == 'prokka'
        run: |
          echo "## STEP 4: Gene Annotation with Prokka"
          echo ""
          echo "### Command to run on HPC:"
          echo '```bash'
          echo 'prokka --prefix my_strain \\'
          echo '       --outdir prokka_output \\'
          echo '       --cpus 32 \\'
          echo '       --kingdom Bacteria \\'
          echo '       spades_output/contigs.fasta'
          echo '```'
          echo ""
          echo "### What to look for in output:"
          echo "- Check prokka_output/my_strain.gff (gene features)"
          echo "- Check prokka_output/my_strain.faa (protein sequences)"
          echo "- Run: grep -c CDS prokka_output/my_strain.gff"
          echo "- Typical bacterial genome: 3,000-5,000 CDS"
          echo "- Review my_strain.txt for summary statistics"

      - name: Summary & Next Steps
        if: github.event.inputs.step == 'all'
        run: |
          echo ""
          echo "## Full Pipeline Summary"
          echo "1. Fastp removes low-quality reads and adapters"
          echo "2. SPAdes assembles clean reads into contigs"
          echo "3. GTDB-Tk classifies your strain taxonomically"
          echo "4. Prokka annotates genes in your assembly"
          echo ""
          echo "### Key output files to save:"
          echo "- contigs.fasta (assembly)"
          echo "- summary.tsv (GTDB classification)"
          echo "- .gff & .faa files (Prokka annotation)"
