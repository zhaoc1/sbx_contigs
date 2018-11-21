# Sunbeam Contigs extension

This is an extension to select [assembled contigs](https://github.com/sunbeam-labs/sunbeam/blob/dev/rules/assembly/assembly.rules) based on the taxonomy annotation by parsing the [blastn summary](https://github.com/sunbeam-labs/sunbeam/blob/dev/rules/annotation/annotation.rules). For example, we are interested in get all the contigs annotated as a give taxon of interest (e.g. Escherichia coli), and later on assess the metagenomics assembled genomes (MAG) quality using [checkM](https://github.com/Ecogenomics/CheckM).

## Installing

With you sunbeam conda environment activated, simply clone the extension directory into the sunbeam/extensions/ folder, installing requirements, and add new options to you existing configuration file:

  ```bash
   git clone https://github.com/sunbeam-labs/sbx_contigs.git extensions/sbx_contigs
   conda install --file extensions/sbx_contigs/requirements.txt
   cat extensions/sbx_contigs/config.yml >> sunbeam_config.yml
   ```

You also need to follow the instructions from the R package [taxonomizr](https://github.com/sherrillmix/taxonomizr) to download and build the database, and specify the *path/to/the/db* in the **config.yml** file.

## Running

There are a few steps in this extension:
1. Using [taxonomizr](https://github.com/sherrillmix/taxonomizr), we can convert the **accession number** per each contig to **taxaID**, and further to **taxonomic name**. Then we can subset contigs by the given taxa of interest.

2. Not all samples will have the taxa of interest. Instead of making the rules super robust, I decide to make my life easier by manually subset the sample.tsv, using the *reports.txt* generated by `contigs_by_taxa` rule.

  ```bash
  sunbeam run --configfile=sunbeam_config.yml contigs_by_taxa
  ```
3. Now we can check the *reports.txt* to see the sample distribution and update the *samples.csv* to *samples.contigs.csv*.

  ```bash
  grep -Fwf <(cut -f1 sunbeam_output/annotation/taxaName/reports.txt | uniq | sort -u) samples.csv > samples.${taxa}.csv
  ```
