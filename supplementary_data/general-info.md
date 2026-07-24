# GENERAL INFORMATION

* Title of Dataset: Additional supplementary data: Data mining of global sequence datasets markedly expands known diversity within the Tobamovirus genus

This repository holds the supplementary data files that accompany the manuscript *Data mining of global sequence datasets markedly expands known diversity within the Tobamovirus genus* and are too large to include in its Supplementary Information document:

- **Supplementary Tables S3–S6** (`Supplementary_table3.tsv` – `Supplementary_table6.tsv`)
- **Supplementary Files S1–S4** (the four `*_tree_alignment.fasta` tree alignments)

The rest of the supplementary material (Supplementary Methods, Supplementary Results, Supplementary Tables S1–S2, and all Supplementary Figures) is in the Supplementary Information document published with the manuscript, not in this repository.

## Submitting Author Information

- Name: Neža Pajek Arambašič
- ORCID: 0009-0002-5433-7816
- Institution: University of Ljubljana, Faculty of Computer and Information Science
- Address: Večna pot 113, 1000 Ljubljana
- Email: neza.pajekarambasic@fri.uni-lj.si

## Corresponding Author / Principal Investigator Information

- Name: Denis Kutnjak
- ORCID: 0000-0002-5327-0587
- Institution: National Institute of Biology
- Address: Večna pot 121, 1000 Ljubljana
- Email: denis.kutnjak@nib.si

* Information about funding sources that supported the collection of the data: This work was supported by the project grant L4-3179 (Discovery and water-linked epidemiology of emergent tobamoviruses infecting crops), financed by the Slovenian Research and Innovation Agency (ARIS), the Ministry of Agriculture, Forestry and Food of the Republic of Slovenia, and BIA d.o.o. The work was also supported by the project grant GC-0001 (Artificial Intelligence for Science) and the core funding P4-0165 (Biotechnology and Plant Systems Biology), both funded by ARIS. 

# SHARING/ACCESS INFORMATION

* Licenses/restrictions placed on the data: CC-BY 4.0
* Links to publications that cite or use the data: https://doi.org/10.6084/m9.figshare.30999190, https://doi.org/10.6084/m9.figshare.30999328, manuscript not yet submitted
* Links to other publicly accessible locations of the data: Novel sequences identified in this study have been deposited in GenBank; accession numbers are listed in the `genbank_accession_number` column of Supplementary_table5.tsv.
* Was data derived from another source? Yes
	* If yes, list source(s): NCBI Sequence Read Archive (SRA) — public sequencing runs identified via Serratus PalmID search (see "Description of methods" below).
* Recommended citation for this dataset: [pending manuscript submission]

# DATA & FILE OVERVIEW

## File List: 
- Supplementary_table3.tsv - Serratus PalmID results from the initial query of 35 tobamoviral palmprints 
- Supplementary_table4.tsv - Metadata for 253 selected SRA accessions
- Supplementary_table5.tsv - 510 selected contigs, including sequences, ORF breakdowns, model predictions, ground truths, and metadata
- Supplementary_table6.tsv - Reference database used for model training
- RdRp-ORF1_tree_alignment.fasta - Trimmed alignment of cluster representatives, reference sequences, and outgroup for maximum-likelihood tree inference from RdRp ORF1
- RdRp-ORF2_tree_alignment.fasta - Trimmed alignment of cluster representatives, reference sequences, and outgroup for maximum-likelihood tree inference from RdRp ORF2
- MP_tree_alignment.fasta - Trimmed alignment of cluster representatives, reference sequences, and outgroup for maximum-likelihood tree inference from MP
- CP_tree_alignment.fasta - Trimmed alignment of cluster representatives, reference sequences, and outgroup for maximum-likelihood tree inference from CP

* Relationship between files, if important: The files form a pipeline, each
  narrowing or building on the one before it. Supplementary_table3.tsv is the
  initial Serratus PalmID search (136,964 SRA hits); Supplementary_table4.tsv
  holds metadata for the 253 SRA runs selected from that search for assembly
  (`run_accession` corresponds to `run_id` in table 3).
  Supplementary_table5.tsv lists the 510 candidate contigs assembled from
  those 253 runs (`corresponding_srr` links back to table 4's
  `run_accession`), including ML predictions and curated ground-truth labels.
  Supplementary_table6.tsv is the independent reference database
  (Virgaviridae + outgroup) used both to train that model and to build the
  phylogenetic trees. The four `*_tree_alignment.fasta` files are trimmed
  alignments combining cluster representatives from table 5
  (`orf*_tree_representative` = TRUE) with the reference/outgroup sequences from
  table 6, one alignment per ORF region.

* Additional related data collected that was not included in the current data package: The Snakemake assembly and candidate-contig-discovery pipeline ([tobamo-snakemake](https://github.com/nezapajek/tobamo-snakemake)) and the downstream dataset-discovery, curation, clustering/phylogenetic-placement, and machine-learning analysis code ([tobamo-analysis](https://github.com/nezapajek/tobamo-analysis)) are maintained as companion GitHub repositories and are not included in this data package.
* Are there multiple versions of the dataset? No

# METHODOLOGICAL INFORMATION

## Description of methods used for collection/generation of data: 

The dataset used in this study was collected by mining publicly available high-throughput sequencing repositories for sequences related to the genus _Tobamovirus_. To identify candidate datasets, viral RNA-dependent RNA polymerase (RdRp) amino acid sequences from thirty-five ICTV-recognized Tobamovirus species were queried using the Serratus PalmID tool (Edgar et al., 2022). Search results were filtered to retain only Sequence Read Archive (SRA) runs with a percentage identity (pident) between 50% and 95% to exclude both known species and weakly related matches. 

This process identified 253 SRA libraries, which were downloaded using the SRA Toolkit (v3.0.2) fasterq-dump utility. Associated metadata for these runs were systematically gathered from NCBI records using pysradb (Choudhary, 2019). For model training purposes, a reference database was constructed by collecting all NCBI whole-genome sequences assigned to the family _Virgaviridae_ as of January 20, 2025.


## Methods for processing the data: 

The collected raw short-read data were processed using an automated Snakemake workflow (v7.32.4) designed for reproducibility. 

The processing steps included:
- Quality Control: Reads were trimmed and filtered using Trimmomatic (v0.39).
- De novo Assembly: Reads were assembled into contigs using SPAdes (v3.15.4) or MEGAHIT (v1.2.9).
- Initial Filtering: Assembled contigs were filtered to retain only those between 600 and 8,000 nucleotides in length.
- Similarity Searches and Taxonomy: Contigs underwent similarity searches against a custom tobamoviral protein database and the NCBI non-redundant (nr) database using DIAMOND blastx.
- Preliminary taxonomic assignment was performed using the lowest common ancestor (LCA) algorithm in Megan6 (v6.21.7), after which cellular-derived sequences were removed.


The candidate contigs were then processed through two complementary paths:
- Manual Curation: Sequences were manually inspected using BLASTN/BLASTX against NCBI databases, with open reading frame (ORF) prediction performed via SnapGene (v7.2.1).
- Machine Learning Framework: A supervised model was developed to automate curation. ORFs were extracted using orfipy and six-frame translation, followed by pairwise alignment using MAFFT (v7.520) against reference sequences. A Random Forest classifier was used to generate ORF-level predictions based on 148 alignment-derived and sequence-based features. These predictions were then aggregated into contig-level classifications using a stacked generalization strategy (a "Binned" Logistic Regression meta-classifier).
  
Finally, putative novel sequences were clustered using NetworkX based on all-vs-all BLASTN results and characterized through phylogenetic inference using MUSCLE, IQ-TREE, and EPA-ng. 

## Instrument- or software-specific information needed to interpret the data: 

* Standards and calibration information, if appropriate: Not a single standard — source sequencing runs come from many independent public studies, each following their own protocols; see per-run details in Supplementary_table4.tsv where available.
* Environmental/experimental conditions: Not applicable as a single condition — samples originate from many independent public studies with heterogeneous conditions; see per-run metadata (e.g. `isolation_source`, `env_broad_scale`, `host`) in Supplementary_table4.tsv.
* Describe any quality-assurance procedures performed on the data: Reads were quality-controlled (adapter/quality trimming via Trimmomatic) as part of the tobamo-snakemake pipeline; candidate contigs flagged by the automated pipeline then underwent manual curation/review before inclusion in Supplementary_table5.tsv.
* People involved with sample collection, processing, analysis and/or submission: Manual curation of candidate contigs: Katarina Bačnik, Luka Kranjc, Lana Vogrinec.

# DATA-SPECIFIC INFORMATION FOR: Supplementary_table3.tsv
* Number of variables: 15
* Number of cases/rows: 136964
* Variable List: 

| Variable | Description | Unit | Value labels / ranges |
| --- | --- | --- | --- |
| run_id | Sequencing run accession | n/a | free text / categorical (6870 unique values) |
| palm_id | Palmprint cluster identifier | n/a | free text / categorical (1703 unique values) |
| coverage | Read coverage | unitless | [0.45, 40683] |
| sOTU | sub-operational taxonomic unit identifier | n/a | free text / categorical (1165 unique values) |
| qseqid | Query sequence identifier | n/a | free text / categorical (35 unique values) |
| pident | Percent identity of the alignment | unitless | [0, 100] |
| evalue | Alignment expectation value | unitless | [0, 1] |
| sra_sequence | SRA palmprint sequence | n/a | free text / categorical (1748 unique values) |
| biosample_id | BioSample accession identifier | n/a | free text / categorical (5931 unique values) |
| scientific_name | Scientific name | n/a | free text / categorical (1009 unique values) |
| nickname | Nickname | n/a | free text / categorical (1165 unique values) |
| date | Collection or processing date | ISO-8601 | [2012-01-21, 2021-01-11] |
| lng | Longitude | degrees | [-180, 180] |
| lat | Latitude | degrees | [-90, 90] |
| file | Source file name or path | n/a | free text / categorical (35 unique values) |
* Missing data codes: 
  - NaN/blank parsed by pandas: found 87167 value(s)
* Specialized formats or other abbreviations used: 
  - none specified

# DATA-SPECIFIC INFORMATION FOR: Supplementary_table4.tsv

Most variables below are standard NCBI/INSDC BioSample attributes, harvested
as-is via pysradb; Description text is abbreviated with "..." where the
official definition is longer — see the [NCBI BioSample Attribute
Dictionary](https://www.ncbi.nlm.nih.gov/biosample/docs/attributes/) for full
wording and controlled vocabularies.
Variables marked "submitter-defined attribute" are not in that dictionary:
they were named by the original data submitters, so their descriptions here
are inferred from the attribute name and the values.

* Number of variables: 164
* Number of cases/rows: 253
* Variable List:

| Variable | Description | Unit | Value labels / ranges |
| --- | --- | --- | --- |
| run_accession | SRA run accession (SRR/ERR/DRR) | n/a | free text / categorical (253 unique values) |
| study_accession | SRA study accession (SRP/ERP/DRP) | n/a | free text / categorical (69 unique values) |
| study_title | Study title | n/a | free text / categorical (69 unique values) |
| experiment_accession | SRA experiment accession (SRX/ERX/DRX) | n/a | free text / categorical (251 unique values) |
| experiment_title | Experiment title | n/a | free text / categorical (149 unique values) |
| experiment_desc | Experiment description | n/a | free text / categorical (149 unique values) |
| organism_taxid | NCBI taxonomic identifier for the organism | unitless | [2708, 2696922] |
| organism_name | Organism name | n/a | free text / categorical (51 unique values) |
| library_name | Submitter-provided library name | n/a | free text / categorical (198 unique values) |
| library_strategy | Sequencing strategy used for the library (SRA controlled vocabulary) | n/a | {'RNA-Seq', 'WGA', 'WGS'} |
| library_source | Type of source material sequenced (SRA controlled vocabulary) | n/a | {'GENOMIC', 'METAGENOMIC', 'METATRANSCRIPTOMIC', 'TRANSCRIPTOMIC', 'TRANSCRIPTOMIC SINGLE CELL'} |
| library_selection | Method used to select or enrich the material sequenced (SRA controlled vocabulary) | n/a | free text / categorical (11 unique values) |
| library_layout | Read layout of the library: single-end (SINGLE) or paired-end (PAIRED) | n/a | {'PAIRED', 'SINGLE'} |
| sample_accession | SRA sample accession (SRS/ERS/DRS) | n/a | free text / categorical (247 unique values) |
| sample_title | Sample title | n/a | n/a |
| biosample | BioSample accession identifier | n/a | free text / categorical (247 unique values) |
| bioproject | BioProject accession identifier | n/a | free text / categorical (69 unique values) |
| instrument | Sequencing instrument model | n/a | free text / categorical (11 unique values) |
| instrument_model | Sequencing instrument model (same values as `instrument`) | n/a | free text / categorical (11 unique values) |
| instrument_model_desc | Sequencing platform | n/a | {'ILLUMINA'} |
| total_spots | Total number of spots (reads or read pairs) across all runs of the experiment | unitless | [102819, 579301609] |
| total_size | Total size of the SRA archive files across all runs of the experiment | bytes | [32997680, 40536321265] |
| run_total_spots | Number of spots (reads or read pairs) in the run | unitless | [102819, 170209268] |
| run_total_bases | Number of bases in the run | unitless | [51615138, 36597608770] |
| run_alias | Submitter-provided run alias | n/a | free text / categorical (250 unique values) |
| public_filename | Publicly available file name | n/a | free text / categorical (253 unique values) |
| public_size | Size of the public file | bytes | [13246524, 23590600282] |
| public_date | Date and time the public file was made available | ISO-8601 | [2015-05-28, 2022-09-29] |
| public_md5 | MD5 checksum for the public file | n/a | free text / categorical (253 unique values) |
| public_version | Version number of the public file | unitless | [1, 2] |
| public_semantic_name | Public file format label | n/a | {'SRA Lite', 'SRA Normalized', 'cram', 'fastq'} |
| public_supertype | Public file distribution class | n/a | {'Original', 'Primary ETL'} |
| public_sratoolkit | Flag indicating whether the file is accessible via SRA Toolkit | unitless | {0, 1} |
| aws_url | Amazon Web Services URL | n/a | free text / categorical (253 unique values) |
| aws_free_egress | Amazon Web Services free-egress region or policy | n/a | {'-', 's3.us-east-1', 'worldwide'} |
| aws_access_type | Amazon Web Services access type | n/a | {'Use Cloud Data Delivery', 'anonymous', 'aws identity'} |
| public_url | Download URL of the public file | n/a | free text / categorical (253 unique values) |
| ncbi_url | NCBI download URL | n/a | free text / categorical (253 unique values) |
| ncbi_free_egress | NCBI free-egress policy | n/a | {'worldwide'} |
| ncbi_access_type | NCBI access type | n/a | {'anonymous'} |
| gcp_url | Google Cloud Storage URL | n/a | free text / categorical (253 unique values) |
| gcp_free_egress | Google Cloud free-egress region or policy | n/a | {'gs.us-east1'} |
| gcp_access_type | Google Cloud access type | n/a | {'gcp identity'} |
| experiment_alias | Submitter-provided experiment alias | n/a | free text / categorical (19 unique values) |
| strain | microbial or eukaryotic strain name | n/a | {'C57BL/6', 'MdSGHV', 'fenpropathrin-resistant', 'fenpropathrin-susceptible'} |
| isolate | identification or description of the specific individual from which this sample was obtained | n/a | free text / categorical (33 unique values) |
| host | The natural (as opposed to laboratory) host to the organism from which the sample was obtained. Use the full... | n/a | free text / categorical (16 unique values) |
| isolation_source | Describes the physical, environmental and/or local geographical source of the biological sample from which the... | n/a | free text / categorical (20 unique values) |
| collection_date | the date on which the sample was collected; date/time ranges are supported by providing two dates from among the... | ISO-8601 | [2008-07-06, 2019-01-01] |
| geo_loc_name | Geographical origin of the sample; use the appropriate name from this list... | n/a | free text / categorical (48 unique values) |
| sample_type | Sample type, such as cell culture, mixed culture, tissue sample, whole organism, single cell, metagenomic assembly | n/a | {'2786_HFKKJALXX_L5', 'cells', 'single cell'} |
| host_tissue_sampled | name of body site where the sample was obtained from, such as a specific organ or tissue, e.g., tongue, lung. For... | n/a | {'Gut'} |
| biosamplemodel | BioSample package (model) under which the sample was registered | n/a | {'Human', 'Invertebrate', 'MIGS/MIMS/MIMARKS.human-gut', 'MIGS/MIMS/MIMARKS.water', 'Metagenome or environmental', ..... |
| lat_lon | The geographical coordinates of the location where the sample was collected. Specify as degrees latitude and... | degrees | formatted latitude/longitude pair |
| breed | breed name - chiefly used in domesticated animals or plants | n/a | {'Missing', 'bashibai sheep', 'citrus', 'not applicable', 'not collected'} |
| tissue | Type of tissue the sample was taken from. | n/a | free text / categorical (32 unique values) |
| gold ecosystem classification | Ecosystem classification path from the Genomes OnLine Database (GOLD) (submitter-defined attribute) | n/a | {'Engineered \| Bioreactor \| Anaerobic \| Digestate \| Unclassified', 'Environmental \| Terrestrial \| Soil \| Floodplain \|... |
| cultivar | cultivar name - cultivated variety of plant | n/a | free text / categorical (14 unique values) |
| age | age at the time of sampling; relevant scale depends on species and study, e.g. could be seconds for amoebae or... | n/a | free text / categorical (16 unique values) |
| biomaterial_provider | name and address of the lab or PI, or a culture collection identifier | n/a | {'Carolina Biological Supply Company', 'Hui-Ling Liao', 'Mark Coggeshall, University of Missouri', 'Prof. Dr. med Geo... |
| collected_by | Name of persons or institute who collected the sample | n/a | {'Dr Brett Williams', 'Gretchen A Gerrish, James G Morin, Nicholai M Hensley, Trevor J Rivers, ...} (10 levels) |
| biological_replicate | biological replicate number; a biological replicate is a sample taken from a different individual or experimental... | n/a | [1, 3] |
| replicate | Replicate number or label (submitter-defined attribute) | n/a | free text / categorical (12 unique values) |
| samp_mat_process | Processing applied to the sample during or after isolation | n/a | {'Total RNA was extracted from each individual using Trizol-Chloroform extractions', 'none'} |
| samp_size | Amount or size of sample (volume, mass or area) that was collected | n/a | {'45 individuals'} |
| ref_biomaterial | Primary publication or genome report | n/a | n/a |
| rel_to_oxygen | Is this organism an aerobe, anaerobe? Please note that aerobic and anaerobic are valid descriptors for microbial... | n/a | n/a |
| samp_collect_device | Method or device employed for collecting sample | n/a | n/a |
| ecotype | a population within a given species displaying genetically based, phenotypic traits that reflect adaptation to a... | n/a | {'B73', 'mising', 'not applicable', 'not collected'} |
| identified_by | name of the taxonomist who identified the specimen | n/a | {'Gretchen A Gerrish, James G Morin, Nicholai M Hensley, Trevor J Rivers, Todd H Oakley, ...} (6 levels) |
| sex | physical sex of sampled organism | n/a | {'female', 'male', 'not collected', 'not determined'} |
| dev_stage | Developmental stage at the time of sampling. | n/a | free text / categorical (14 unique values) |
| ena-first-public | ENA first public release date | n/a | {'2018-06-11 19:03:00', '2019-02-08 18:04:04', '2019-02-26 18:02:58', '2019-04-19 06:02:37'} |
| ena-last-update | ENA last update date | n/a | {'2018-06-05 17:33:43', '2018-06-22 15:33:29', '2018-12-17 15:58:17', '2019-02-21 10:18:26'} |
| external id | External sample accession cross-referenced by the record (here BioSample accessions) | n/a | {'SAMEA3923215', 'SAMEA4708865', 'SAMEA4753698', 'SAMEA5185551', 'SAMEA5365958'} |
| insdc center name | Name of the submitting center as registered with INSDC | n/a | {'Center for Biotechnology (CeBiTec) - Bielefeld University', 'EAWAG', 'Faculty of Medicine, Goethe-University Frankf... |
| insdc first public | INSDC first public release date | n/a | {'2018-03-30 19:02:14', '2018-06-11 19:03:00', '2019-02-08 18:04:04', '2019-02-26 18:02:58', '2019-04-19 06:02:37'} |
| insdc last update | INSDC last update date | n/a | {'2016-10-21 11:43:06', '2018-06-05 17:33:43', '2018-06-22 15:33:29', '2018-12-17 15:58:17', '2019-02-21 10:18:26'} |
| insdc status | Release status of the record at INSDC | n/a | {'public'} |
| submitter id | Submitter-assigned sample identifier | n/a | {'20730x94', '6bcb1c40-540d-11e6-9a3d-3c4a9275d6c8', 'E-MTAB-6943:IgG-ACM_1, 2, 3', ...} (7 levels) |
| common name | Common name of the sampled organism | n/a | {'human', 'viral metagenome'} |
| sample name | sample name in source database | n/a | {'20730x94', '6bcb1c40-540d-11e6-9a3d-3c4a9275d6c8', 'E-MTAB-6943:IgG-ACM_1, 2, 3', ...} (7 levels) |
| scientific_name | Scientific name of the sampled organism | n/a | {'Homo sapiens', 'soil metagenome', 'viral metagenome'} |
| phenotype | Phenotype of sampled organism. For Phenotypic quality Ontology (PATO) (v1.269) terms, please see... | n/a | {'cyan gray', 'white', 'wild type phenotype'} |
| experimental factor: compound | ArrayExpress experimental factor: compound applied to the sample (submitter-defined attribute) | n/a | {'ryegrass silage'} |
| experimental factor: dose | ArrayExpress experimental factor: dose of the compound applied (submitter-defined attribute) | not stated in source record | [1500, 1500] |
| ebi_url | EBI download URL | n/a | {'http://ftp.sra.ebi.ac.uk/vol1/run/ERR135/ERR1356733/RU122R2.fq.gz', 'http://ftp.sra.ebi.ac.uk/vol1/run/ERR266/ERR26... |
| ebi_free_egress | EBI free-egress policy | n/a | {'worldwide'} |
| ebi_access_type | EBI access type | n/a | {'anonymous'} |
| broker name | Name of the broker that submitted the record on the submitter's behalf | n/a | {'ArrayExpress'} |
| altitude | The altitude of the sample is the vertical distance between Earth's surface above Sea Level and the sampled... | n/a | n/a |
| env_broad_scale | Add terms that identify the major environment type(s) where your sample was collected. Recommend subclasses of... | unitless | environment (broad scale) |
| env_local_scale | Add terms that identify environmental entities having causal influences upon the entity at time of sampling,... | unitless | environment (local scale) |
| env_medium | Add terms that identify the material displaced by the entity at time of sampling. Recommend subclasses of... | unitless | environment (medium scale) |
| season | The season when sampling occurred. Any of the four periods into which the year is divided by the equinoxes and... | n/a | n/a |
| type_of_nucleic_acid | Type of nucleic acid sequenced (submitter-defined attribute) | n/a | n/a |
| arrayexpress-species | Species name as recorded in ArrayExpress | n/a | {'viral metagenome'} |
| sample description | Free-text description of the sample | n/a | {'Total nucleic acid extraction from human feces'} |
| experimental factor: growth condition | ArrayExpress experimental factor: growth condition (submitter-defined attribute) | n/a | {'MCF-7 conditioned media'} |
| experimental factor: stimulus | ArrayExpress experimental factor: stimulus applied to the sample (submitter-defined attribute) | n/a | {'none'} |
| experimental factor: immunoprecipitate | ArrayExpress experimental factor: antibody used for immunoprecipitation (submitter-defined attribute) | n/a | {'anti-IgG'} |
| cell type | Type of cell of the sample or from which the sample was obtained. | n/a | {'S2 cells', 'macrophage'} |
| developmental stage | Developmental stage at the time of sampling. | n/a | {'adult'} |
| genotype | observed genotype | n/a | {'PB1+ Pi54', 'Wildtype', 'wild type genotype'} |
| growth condition | Growth condition of the sampled cells (submitter-defined attribute) | n/a | {'MCF-7 conditioned media'} |
| indivudal | Individual(s) the sample was taken from; the attribute name is misspelled in the source record (submitter-defined attribute) | n/a | {'pool of donors 1 to 3'} |
| organism part | Type of tissue the sample was taken from. | n/a | {'blood'} |
| progenitor cell type | Cell type from which the sampled cells were derived (submitter-defined attribute) | n/a | {'peripheral blood mononuclear cell'} |
| source_name | Sample source name or description | n/a | {'Cortical thymic epithelial cells', 'Leaf', 'Medullary thymic epithelial cells', 'S2 cells', 'Skin epithelial cells'... |
| treatment | treatment applied to the sample, such as chemical treatment, mechanical wounding, rehydration, etc. Please note... | n/a | free text / categorical (79 unique values) |
| sample_name | sample name in source database | n/a | {'Sample_1086', 'Sample_1235'} |
| elev | The elevation of the sampling site as measured by the vertical distance from mean sea level. | n/a | elevation |
| env_feature | Add terms that identify environmental entities having causal influences upon the entity at time of sampling,... | n/a | {'Amazon River', 'ENVO:2100002'} |
| depth | Depth is defined as the vertical distance below surface, e.g. for sediment or soil samples depth is measured from... | m in text | {'0.5 m', '10 m', '14 m', '33 m'} |
| env_material | Add terms that identify the material displaced by the entity at time of sampling. Recommend subclasses of... | n/a | {'ENVO:00002003', 'water'} |
| env_biome | Add terms that identify the major environment type(s) where your sample was collected. Recommend subclasses of... | n/a | {'ENVO:00009003', 'river'} |
| ena first public | ENA first public release date | n/a | {'2018-03-30'} |
| ena last update | ENA last update date | n/a | {'2016-10-21'} |
| insdc center alias | Short alias of the submitting center as registered with INSDC | n/a | {'EAWAG'} |
| collection date | the date on which the sample was collected; date/time ranges are supported by providing two dates from among the... | ISO-8601 | [2014, 2014] |
| environment (biome) | Add terms that identify the major environment type(s) where your sample was collected. Recommend subclasses of... | n/a | {'wastewater'} |
| environment (feature) | Add terms that identify environmental entities having causal influences upon the entity at time of sampling,... | n/a | {'wastewater treatment plant'} |
| environment (material) | Add terms that identify the material displaced by the entity at time of sampling. Recommend subclasses of... | n/a | {'activated sludge'} |
| geographic location (country and/or sea) | Geographical origin of the sample; use the appropriate name from this list... | n/a | {'Switzerland'} |
| geographic location (latitude) | Sampling latitude | degrees | [-90, 90] |
| geographic location (longitude) | Sampling longitude | degrees | [-180, 180] |
| investigation type | Nucleic Acid Sequence Report is the root element of all MIGS/MIMS compliant reports as standardized by Genomic... | n/a | {'metagenome'} |
| project name | A concise name that describes the overall project, for example "Analysis of sequences collected from Antarctic soil" | n/a | {'What biological and operational factors determine the release of antibiotic resistance genes from WWTPs?'} |
| sequencing method | Sequencing method as stated by the submitter | n/a | {'Illumina HiSeq'} |
| wastewater/sludge environmental package | MIxS environmental package assigned to the sample | n/a | {'wastewater/sludge'} |
| cell_line | Name of the cell line. | n/a | {'P493-4', 'P493-5', 'P493-6'} |
| cell_type | Type of cell of the sample or from which the sample was obtained. | n/a | {'B-cell'} |
| nucleic_acid | Type of nucleic acid extracted (submitter-defined attribute) | n/a | {'rna'} |
| sample_timepoint | Sampling time point label (submitter-defined attribute) | n/a | {'SF05'} |
| host_subject_id | a unique identifier by which each subject can be referred to, de-identified, e.g. #131 | n/a | [50176181, 50176181] |
| isolation source | Describes the physical, environmental and/or local geographical source of the biological sample from which the... | n/a | n/a |
| isol_growth_condt | PMID or url for isolation and growth condition specifications | unitless | n/a |
| propagation | phage: lytic/lysogenic/temperate/obligately lytic; plasmid: incompatibility group; eukaryote: asexual/sexual | n/a | n/a |
| number_of_cells | Number of cells used as input for the sample (submitter-defined attribute) | unitless | [500000, 2000000] |
| target_molecules | Type of molecule targeted for sequencing (submitter-defined attribute) | n/a | {'mRNA'} |
| site_name | Name of the sampling site (submitter-defined attribute) | n/a | {'Macapa North (MCPN)', 'Macapa South (MCPS)', 'Obidos (OB)', 'Tapajos Surface (TAPS)'} |
| chlorophyll | concentration of chlorophyll | n/a | {'0.87 ug/L', '1.67 ug/L', '1.99 ug/L', '3.23 ug/L', '3.82 ug/L'} |
| conduc | electrical conductivity of water | uS/cm in text | {'15.9 uS/cm', '55.3 uS/cm', '56.2 uS/cm', '56.4 uS/cm', '61.5 uS/cm'} |
| diss_inorg_carb | dissolved inorganic carbon concentration | umol C/kg in text | {'155 umol C/kg', '459 umol C/kg', '502 umol C/kg', '507 umol C/kg', '551 umol C/kg'} |
| bacterial_count | Bacterial cell concentration in the sampled water (submitter-defined attribute) | cells/mL in text | {'3.58E06 cells/mL', '3.63E06 cells/mL', '3.76E06 cells/mL', '3.94E06 cells/mL', '3.96E06 cells/mL'} |
| samp_volume | Volume of water sampled (submitter-defined attribute) | L (liters) in text format | {'0.65 L', '0.7 L', '0.73 L', '0.76 L', '1.06 L', ...} (6 levels) |
| min_filter_size | Smallest filter pore size used to collect the sample (submitter-defined attribute) | um in text | {'0.2 um', '2 um', '2.0 µm'} |
| max_filter_size | Largest filter pore size used to collect the sample (submitter-defined attribute) | um in text | {'156 µm', '2.0 um', '297 um'} |
| sequencing_method | Sequencing method as stated by the submitter | n/a | {'Illumina PE 150x150', 'Illumina Rapid Run'} |
| sequencing_machine | Sequencing instrument as stated by the submitter | n/a | {'HiSeq 2500'} |
| std_norm_factor | Normalization factor reported by the submitter, apparently derived from internal standards (submitter-defined attribute) | unitless | [4217, 43832] |
| specimen_voucher | Identifier for the physical specimen. Use format: "[\<institution-code\>:[\<collection-code\>:]]\<specimen_id\>"... | n/a | {'TH-08-6182'} |
| source_material_id | unique identifier assigned to a material sample used for extracting nucleic acids, and subsequent sequencing. The... | n/a | n/a |
| sample_number | Sample number (submitter-defined attribute) | unitless | n/a |
| sample_source | Source of the sample (submitter-defined attribute) | n/a | n/a |
| investigation_type | Nucleic Acid Sequence Report is the root element of all MIGS/MIMS compliant reports as standardized by Genomic... | n/a | {'Non-selective Metatranscriptome'} |
| temp | temperature of the sample at time of sampling | n/a | {'AMBIENT'} |
| geographic_location | Geographical origin of the sample; use the appropriate name from this list... | n/a | {'not collected'} |
| ena_fastq_http | ENA HTTP download URL of the FASTQ file (runs with a single FASTQ file) | n/a | free text / categorical (131 unique values) |
| ena_fastq_http_1 | ENA HTTP download URL of the read 1 FASTQ file (runs with paired FASTQ files) | n/a | free text / categorical (122 unique values) |
| ena_fastq_http_2 | ENA HTTP download URL of the read 2 FASTQ file (runs with paired FASTQ files) | n/a | free text / categorical (121 unique values) |
| ena_fastq_ftp | ENA Aspera (era-fasp) path of the FASTQ file (runs with a single FASTQ file) | n/a | free text / categorical (131 unique values) |
| ena_fastq_ftp_1 | ENA Aspera (era-fasp) path of the read 1 FASTQ file (runs with paired FASTQ files) | n/a | free text / categorical (122 unique values) |
| ena_fastq_ftp_2 | ENA Aspera (era-fasp) path of the read 2 FASTQ file (runs with paired FASTQ files) | n/a | free text / categorical (121 unique values) |
* Missing data codes:
  - 'none': found 20 value(s)
  - 'Unknown': found 1 value(s)
  - 'unknown': found 2 value(s)
  - NaN/blank parsed by pandas: found 27738 value(s)
* Specialized formats or other abbreviations used: 
  - datetime columns: public_date, ena-first-public, ena-last-update, insdc first public, insdc last update, ena first public, ena last update

# DATA-SPECIFIC INFORMATION FOR: Supplementary_table5.tsv

* Number of variables: 54
* Number of cases/rows: 510
* Variable List: variable name, description, unit, and value labels/ranges

| Variable | Description | Unit | Value labels / ranges |
| --- | --- | --- | --- |
| contig_id | Contig identifier | n/a | free text / categorical (510 unique values) |
| sequence | Nucleotide sequence | n/a | free text / categorical (510 unique values) |
| corresponding_srr | corresponding SRA Run accession identifier | n/a | free text / categorical (131 unique values) |
| assembler | Assembler name | n/a | {'megahit', 'spades'} |
| cluster_membership | cluster membership identifier | n/a | free text / categorical (56 unique values) |
| known_or_potentially_novel_tobamovirus | flag indicating whether the contig is a known or potentially novel tobamovirus (TRUE) or not (FALSE) | unitless | {TRUE, FALSE} |
| contig_length | Contig length | bp | [606, 7832] |
| cluster_representative | cluster representative flag (TRUE for the representative contig of each cluster, blank otherwise) | unitless | {TRUE} |
| orf1_complete | flag indicating whether ORF1 (RdRp) is complete (TRUE) or not (FALSE) | unitless | {TRUE, FALSE} |
| orf1_partial | flag indicating whether ORF1 (RdRp) is partial (TRUE) or not (FALSE) | unitless | {TRUE, FALSE} |
| orf1_length | ORF1 (RdRp) length | aa | [0, 1135] |
| orf1_start | flag indicating whether the ORF1 (RdRp) start codon is present (TRUE) or not (FALSE) | unitless | {TRUE, FALSE} |
| orf1_stop | flag indicating whether the ORF1 (RdRp) stop codon is present (TRUE) or not (FALSE) | unitless | {TRUE, FALSE} |
| orf1_tree_representative | flag indicating whether the contig is a representative in the ORF1 (RdRp) tree (TRUE) or not (FALSE) | unitless | {TRUE, FALSE} |
| orf2_rdrp_complete | flag indicating whether ORF2 (RdRp) is complete (TRUE) or not (FALSE) | unitless | {TRUE, FALSE} |
| orf2_rdrp_partial | flag indicating whether ORF2 (RdRp) is partial (TRUE) or not (FALSE) | unitless | {TRUE, FALSE} |
| orf2_rdrp_length | ORF2 (RdRp) length | aa | [0, 592] |
| orf2_start | flag indicating whether the ORF2 (RdRp) start codon is present (TRUE) or not (FALSE) | unitless | {TRUE, FALSE} |
| orf2_stop | flag indicating whether the ORF2 (RdRp) stop codon is present (TRUE) or not (FALSE) | unitless | {TRUE, FALSE} |
| orf2_tree_representative | flag indicating whether the contig is a representative in the ORF2 (RdRp) tree (TRUE) or not (FALSE) | unitless | {TRUE, FALSE} |
| orf3_mp_complete | flag indicating whether ORF3 (MP) is complete (TRUE) or not (FALSE) | unitless | {TRUE, FALSE} |
| orf3_mp_partial | flag indicating whether ORF3 (MP) is partial (TRUE) or not (FALSE) | unitless | {TRUE, FALSE} |
| orf3_mp_length | ORF3 (MP) length | aa | [0, 306] |
| orf3_start | flag indicating whether the ORF3 (MP) start codon is present (TRUE) or not (FALSE) | unitless | {TRUE, FALSE} |
| orf3_stop | flag indicating whether the ORF3 (MP) stop codon is present (TRUE) or not (FALSE) | unitless | {TRUE, FALSE} |
| orf3_tree_representative | flag indicating whether the contig is a representative in the ORF3 (MP) tree (TRUE) or not (FALSE) | unitless | {TRUE, FALSE} |
| orf4_cp_complete | flag indicating whether ORF4 (CP) is complete (TRUE) or not (FALSE) | unitless | {TRUE, FALSE} |
| orf4_cp_partial | flag indicating whether ORF4 (CP) is partial (TRUE) or not (FALSE) | unitless | {TRUE, FALSE} |
| orf4_cp_length | ORF4 (CP) length | aa | [0, 186] |
| orf4_start | flag indicating whether the ORF4 (CP) start codon is present (TRUE) or not (FALSE) | unitless | {TRUE, FALSE} |
| orf4_stop | flag indicating whether the ORF4 (CP) stop codon is present (TRUE) or not (FALSE) | unitless | {TRUE, FALSE} |
| orf4_tree_representative | flag indicating whether the contig is a representative in the ORF4 (CP) tree (TRUE) or not (FALSE) | unitless | {TRUE, FALSE} |
| notes | Free-text notes | n/a | {'missing orf3', 'missing stop codon in orf3', 'missing stop codon in orf4', 'missing stop codons in orf2 and orf3',... |
| ground_truth_category | Ground-truth category label | n/a | {'mas', 'oth1', 'oth2', 'tob1', 'tob2', ...} (6 levels) |
| model_prediction | Model prediction label | unitless | {0, 1} |
| model_prediction_probability | Model prediction probability | unitless | [0, 1] |
| first_diamond_blastx_hit_name | first diamond blastx hit name | n/a | free text / categorical (40 unique values) |
| first_diamond_blastx_hit_identity_percent | Percent identity of the top BLASTX hit | % | [2.8, 99.9] |
| first_diamond_blastx_hit_alignment_length_aa | Amino-acid alignment length of the top DIAMOND BLASTX hit | aa | [53, 2810] |
| first_blastx_hit_name_accession | Accession identifier | n/a | free text / categorical (237 unique values) |
| first_blastx_hit_identity_percent | Percent identity of the top BLASTX hit | % | [22, 100] |
| first_blastx_hit_alignment_length_aa | Amino-acid alignment length of the top BLASTX hit | aa | [50, 1889] |
| first_blastx_hit_e_value | E-value of the top BLASTX hit | unitless | [0, 1] |
| first_blastx_hit_bit_score | Bit score of the top BLASTX hit | unitless | [64.3, 3428] |
| first_blastx_hit_protein | First blastx hit protein result | n/a | free text / categorical (212 unique values) |
| study_accession | Study accession identifier | n/a | free text / categorical (57 unique values) |
| study_title | Study title | n/a | free text / categorical (57 unique values) |
| organism_name | Organism name | n/a | free text / categorical (44 unique values) |
| submitter | Submitter name | n/a | free text / categorical (46 unique values) |
| country | Geographical origin of the sample; use the appropriate name from this list... | n/a | free text / categorical (44 unique values) |
| collection_date | the date on which the sample was collected; date/time ranges are supported by providing two dates from among the... | ISO-8601 | [2008-07-06, 2019-01-01] |
| source_sample_category | Broad source sample category | n/a | {'Aquatic', 'Host-associated', 'Terrestrial'} |
| source_sample_subcategory | Source sample subcategory | n/a | {'Animal', 'Animal gut', 'Freshwater', 'Human', 'Human gut', ...} (9 levels) |
| genbank_accession_number | GenBank accession number | n/a | free text / categorical (63 unique values) |
* Missing data codes:
  - NaN/blank parsed by pandas: found 11003 value(s)
* Specialized formats or other abbreviations used: none specified

# DATA-SPECIFIC INFORMATION FOR: Supplementary_table6.tsv

* Number of variables: 16
* Number of cases/rows: 125
* Variable List:

| Variable | Description | Unit | Value labels / ranges |
| --- | --- | --- | --- |
| type | group type | n/a | {'outgroup', 'tobamo'} |
| genus | Taxonomic genus | n/a | {'Furovirus', 'Goravirus', 'Hordeivirus', 'Pecluvirus', 'Pomovirus', ...} (7 levels) |
| species | Taxonomic species | n/a | free text / categorical (60 unique values) |
| virus_name | Virus or viral taxon | n/a | free text / categorical (88 unique values) |
| accession | Accession identifier | n/a | free text / categorical (125 unique values) |
| RNA | Ribonucleic acid fragment | n/a | {'RNA1', 'RNA2', 'RNA3'} |
| training | Training-set membership flag (training 1, not in training 0) | unitless | {0, 1} |
| length | Sequence length | bp | [1799, 7226] |
| orf1_ref | Reference identifier for ORF1 | n/a | free text / categorical (88 unique values) |
| orf2_ref | Reference identifier for ORF2 | n/a | free text / categorical (88 unique values) |
| cp_ref | Reference identifier for coat protein | n/a | free text / categorical (87 unique values) |
| record_id | Genome fasta identifier | n/a | free text / categorical (125 unique values) |
| orf1_record_id | Record identifier for ORF1 | n/a | free text / categorical (88 unique values) |
| orf2_record_id | Record identifier for ORF2 | n/a | free text / categorical (88 unique values) |
| cp_record_id | Record identifier for coat protein | n/a | free text / categorical (87 unique values) |
| sampling_prob | Sampling probability used during training set construction | unitless | [0, 1] |
* Missing data codes:
  - NaN/blank parsed by pandas: found 224 value(s)
* Specialized formats or other abbreviations used: 
  - none specified