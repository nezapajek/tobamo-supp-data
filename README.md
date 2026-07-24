# tobamo-supp-data

Supplementary data files for:

> Pajek Arambašič N, Bačnik K, Kranjc L, Vogrinec L, Curk T, Kutnjak D.
> **Data mining of global sequence datasets markedly expands known diversity
> within *Tobamovirus* genus.** Manuscript in preparation.

> [!IMPORTANT]
> **This repository holds only part of the supplementary material:** the
> items too large for the Supplementary Information PDF, i.e.
> **Supplementary Tables S3–S6** and **Supplementary Files S1–S4**.
>
> Supplementary Methods, Supplementary Results, Supplementary Tables S1–S2
> and Supplementary Figures S1–S9 are in the **Supplementary Information
> PDF published with the article. They are not in this repository.**

This is the third of three repos this project's code/data was split into:

1. [tobamo-analysis](https://github.com/nezapajek/tobamo-analysis) — dataset
   discovery (Serratus PalmID query → 253 candidate SRRs), the step upstream
   of the Snakemake pipeline, plus everything downstream of it: manual
   curation, clustering/phylogenetic placement, and machine-learning
   classification.
2. [tobamo-snakemake](https://github.com/nezapajek/tobamo-snakemake) —
   automated assembly and candidate contig discovery on those SRRs (quality
   control, *de novo* assembly, similarity search, preliminary taxonomic
   assignment).
3. **tobamo-supp-data** (this repo) — Supplementary Tables S3–S6 and
   Supplementary Files S1–S4 referenced by the article; data only, not
   runnable code like the other two.

## Where to find each supplementary item

| Item | Description | Where |
|---|---|---|
| Supplementary Methods | Local tpdb2 and NCBI nr database setup for the Snakemake pipeline; machine learning framework for automated curation of putative tobamoviral contigs | SI PDF |
| Supplementary Results | Detailed machine learning evaluation results | SI PDF |
| Table S1 | Pairwise alignment metrics between ORFs and reference protein sequences | SI PDF |
| Table S2 | Feature importance rankings for the tobamovirus ORF classifier (Random Forest) | SI PDF |
| **Table S3** | Serratus PalmID results from the initial query of 35 tobamoviral palmprints | **this repo:** `Supplementary_table3.tsv` |
| **Table S4** | Metadata for 253 selected SRA accessions | **this repo:** `Supplementary_table4.tsv` |
| **Table S5** | 510 selected contigs: sequences, ORF breakdowns, model predictions, ground truths, metadata | **this repo:** `Supplementary_table5.tsv` |
| **Table S6** | Reference database used for model training | **this repo:** `Supplementary_table6.tsv` |
| **File S1** | Trimmed tree alignment, RdRp ORF1 | **this repo:** `RdRp-ORF1_tree_alignment.fasta` |
| **File S2** | Trimmed tree alignment, RdRp ORF2 | **this repo:** `RdRp-ORF2_tree_alignment.fasta` |
| **File S3** | Trimmed tree alignment, MP | **this repo:** `MP_tree_alignment.fasta` |
| **File S4** | Trimmed tree alignment, CP | **this repo:** `CP_tree_alignment.fasta` |
| Figure S1 | Validation of contig length sampling distribution | SI PDF |
| Figure S2 | Feature enrichment analysis of the top 40 features in the ORF classifier | SI PDF |
| Figure S3 | Model performance comparison across histogram bin configurations | SI PDF |
| Figure S4 | Threshold optimization analysis and performance metrics for the contig classifier | SI PDF |
| Figure S5 | Prediction-probability distributions at ORF and contig level | SI PDF |
| Figures S6–S9 | Maximum likelihood phylogenetic trees of novel tobamoviral sequences inferred from RdRp ORF1, RdRp ORF2, MP and CP | SI PDF |

Table numbering is shared with the PDF: Tables S1–S2 are in the PDF, and
Tables S3–S6 here continue that numbering.

## Contents

General information about the data in this repo (authors, funding, methods,
and a description of every file and variable) is in
[supplementary_data/general-info.md](supplementary_data/general-info.md).

```
supplementary_data/
├── general-info.md
│     # dataset documentation: authors, funding, methods, per-file variable descriptions
├── Supplementary_table3.tsv
│     # Table S3 — Serratus PalmID hits: 35 tobamovirus palmprints queried against all SRA runs (136,964 rows)
├── Supplementary_table4.tsv
│     # Table S4 — metadata for the 253 SRA accessions selected for download (253 rows)
├── Supplementary_table5.tsv
│     # Table S5 — 510 curated candidate contigs: sequences, ORF breakdowns, model predictions, ground-truth labels, metadata (510 rows)
├── Supplementary_table6.tsv
│     # Table S6 — reference database of Virgaviridae + outgroup sequences used for model training and phylogenetic placement (125 rows)
├── RdRp-ORF1_tree_alignment.fasta
│     # File S1 — trimmed alignment (cluster representatives + references + outgroup) for RdRp ORF1 maximum-likelihood tree inference
├── RdRp-ORF2_tree_alignment.fasta
│     # File S2 — same, for RdRp ORF2
├── MP_tree_alignment.fasta
│     # File S3 — same, for the movement protein (MP, ORF3)
└── CP_tree_alignment.fasta
      # File S4 — same, for the coat protein (CP, ORF4)
```

**RdRp = ORF1 + ORF2.** The RNA-dependent RNA polymerase is expressed as a
readthrough of ORF1 into ORF2, so `RdRp-ORF1_tree_alignment.fasta` and
`RdRp-ORF2_tree_alignment.fasta` are the two halves of the RdRp region.

## License

CC-BY 4.0.

## Contact

For questions, please open an issue on GitHub or contact
neza.pajekarambasic@fri.uni-lj.si
