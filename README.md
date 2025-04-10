# Critical Assessment of Protein Intrinsic Disorder (CAID) 

## Reference generation and analysis

This is the official repository of the [Critical Assessment of Protein Intrinsic Disorder (CAID)](https://caid.idpcentral.org/) challenge.

The previous CAID2 repository is available [here](https://github.com/BioComputingUP/caid2-reference).

Follow the instructions below to replicate the generation of the references and some useful statistics.

```bash
# Generate the folder structure
mkdir -p data/{disprot,sifts,alphafold,output/{references,references_stat,references_merge_analysis,homology,new_taxdump}}
````

## references
Generate the references from two snapshots of the DisProt database (mongo export)
DisProt data can be obtained directly exporting the relevant database collections (ask the developers). 
Or using the download service from the website (lastest annotations might not be available to the public). 
Note the formats are slightly different.

## Download DisProt data
Use MongoDB compass and download the current public collection and
the current "curators" collections.

Public 2023_12
Current 2024_12_c (29 Oct 2024)

For the CAID3 CASP-16 dataset
mongoexport --uri "mongodb://moros:27017/disprot8" --collection entries_2024_12_c > data/disprot/disprot8.entries_2024_12_c.json


```bash
# Download data
wget -O data/sifts/uniprot_segments_observed.tsv.gz ftp://ftp.ebi.ac.uk/pub/databases/msd/sifts/flatfiles/tsv/uniprot_segments_observed.tsv.gz
wget -O data/disprot/go-basic.obo http://purl.obolibrary.org/obo/go/go-basic.obo
```

## homology
Parse the blast output, extract information about the best match and perform optimal 
pairwise alignments.
Comparison are between CAID and DisProt "old" and between
CAID and PDB seqres.

```bash
# Generate BLAST alignments of the new DisProt against the old DisProt and against PDB seqres
# Install blast on your home (check the version and paths)
wget https://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/LATEST/ncbi-blast-2.15.0+-x64-linux.tar.gz
tar -xf ncbi-blast-2.15.0+-x64-linux.tar.gz
export PATH="/home/$USER/ncbi-blast-2.15.0+/bin:$PATH" 

# Download PDB seqres
wget https://files.wwpdb.org/pub/pdb/derived_data/pdb_seqres.txt.gz -O data/output/homology/pdb_seqres.txt.gz
gunzip data/output/homology/pdb_seqres.txt.gz

# Make blast dbs
makeblastdb -in data/output/homology/disprot_old.fasta -dbtype prot
makeblastdb -in data/output/homology/pdb_seqres.txt -dbtype prot

# Run BLAST
blastp -db data/output/homology/disprot_old.fasta -query data/output/homology/disprot_new.fasta -out data/output/homology/disprot_new_old.blast -outfmt 6 -num_threads 12
blastp -db data/output/homology/pdb_seqres.txt -query data/output/homology/disprot_new.fasta -out data/output/homology/disprot_new_pdb.blast -outfmt 6 -num_threads 12
```

## homology_plot
Generate plots from the output of the homology notebook


## references_stat
Generate statistics about the references

```bash
# Download taxonomy data
wget -O data/new_taxdump.tar.gz  ftp://ftp.ncbi.nih.gov/pub/taxonomy/new_taxdump/new_taxdump.tar.gz
tar -xf data/new_taxdump.tar.gz -C data/new_taxdump
```
