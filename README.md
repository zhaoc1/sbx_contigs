# Sunbeam Contigs extension [beta]

This is an extension to select [contigs](https://github.com/sunbeam-labs/sunbeam/blob/dev/rules/assembly/assembly.rules) based on the [blast summary](https://github.com/sunbeam-labs/sunbeam/blob/dev/rules/annotation/annotation.rules) for a give taxon of interest, e.g. Escherichia coli, calculate the coverage by mapping reads back and generate coverage plot.

## Installing

With you sunbeam conda environment activated, simply clone the extension directory into the sunbeam/extensions/ folder, installing requirements, and add new options to you existing configuration file:

  ```bash
   git clone https://github.com/sunbeam-labs/sbx_contigs.git extensions/sbx_contigs
   conda install --file extensions/sbx_contigs/requirements.txt
   cat extensions/sbx_contigs/config.yml >> sunbeam_config.yml
   ```
 
You also need to follow the instructions from the [taxonomizr](https://github.com/sherrillmix/taxonomizr) to download and build the database, and specify the path/to/the/db in the contig.yml file.

## Running

There are a few steps in this extension:
1. Using [taxonomizr](https://github.com/sherrillmix/taxonomizr), we can convert the accession number per each contig to taxonomic IDs, and further to taxonomic nameS. Then we can filter/subset contigs by the given taxa of interest.

2. Not all samples will have the taxa of interest. Instead of making the rules super robust, I decide to make my life easier by manually subset the sample.tsv, using the reports.txt generated by `contigs_by_taxa` rule.

  ```bash
  sunbeam run --configfile=sunbeam_config.yml contigs_by_taxa
  ## now we can check the reports.txt to see the sample distribution and update the samples.csv
  grep -Fwf <(cut -f1 sunbeam_output/annotation/taxaName/reports.txt | uniq | sort -u) samples.csv > samples.${taxa}.csv
  ```
  
3. Once we have the selected contigs ready for each sample, we can simply to the reads back to each contigs and get the coverage stats.
 
  ```bash
  sunbeam run --configfile=sunbeam_config.yml _contigs_mapping
  ```

4. Contigs coverage stats
 
    Same with [genome mapping](https://github.com/sunbeam-labs/sunbeam/blob/dev/rules/mapping/mapping.rules), we want two kinds of stats files:
     
     - contigs mapping coverage summary 
     - coverage per base for each sample
     

  
