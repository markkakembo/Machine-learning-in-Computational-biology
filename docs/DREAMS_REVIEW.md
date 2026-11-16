# DreaMS: learning molecular representations from unannotated mass spectra

**Da Vinci's Poet | Research notes | 16 November 2026**

**Scope:** Literature synthesis and commentary on Bushuiev et al.; not original experimental work or an independent reproduction. All numerical results below were reported by the paper's authors.

## The paper

Roman Bushuiev, Anton Bushuiev, Raman Samusevich, Corinna Brungs, Josef Sivic and Tomáš Pluskal. *Self-supervised learning of molecular representations from millions of tandem mass spectra using DreaMS.* **Nature Biotechnology 44, 630–640 (2026)**; published online 23 May 2025. [DOI: 10.1038/s41587-025-02663-3](https://doi.org/10.1038/s41587-025-02663-3). Roman and Anton Bushuiev contributed equally.

## What the authors built

DreaMS—Deep Representations Empowering the Annotation of Mass Spectra—is a 116-million-parameter transformer producing 1,024-dimensional spectral embeddings. It learns from unannotated tandem mass spectra by reconstructing masked peak mass-to-charge values and predicting relative chromatographic elution order. Downstream models are then fine-tuned on annotated spectral libraries. Self-supervision reduces dependence on structural labels during pre-training; it does not remove labels from downstream training. [Paper, Results and Fig. 3](https://www.nature.com/articles/s41587-025-02663-3#Fig3).

The scale requires three distinct counts:

| Resource | Authors' reported scale | Meaning |
| --- | --- | --- |
| GeMS collection | Approximately 700 million spectra extracted from 250,000 LC–MS/MS experiments | Starting collection mined from MassIVE GNPS |
| GeMS-A10 | Approximately 24 million spectra | High-quality, redundancy-reduced pre-training subset |
| DreaMS Atlas | Approximately 34 million representative spectra covering 201 million spectra | Molecular network; neither count denotes confirmed unique molecules |

Sources: [paper, Results, Figs. 2 and 6; Methods, ablation studies and DreaMS Atlas](https://doi.org/10.1038/s41587-025-02663-3).

## Results worth separating

- **Representation learning:** Linear probing and similarity analyses support the emergence of structure-related information during pre-training. Contrastive fine-tuning improves discrimination between similar-mass compounds. These analyses support useful representations, rather than definitive structure assignment from embeddings alone. [Fig. 3; Fig. 4a–c; Extended Data Fig. 5](https://doi.org/10.1038/s41587-025-02663-3).
- **Fingerprint-based retrieval:** On the MIST benchmark, DreaMS performs **on par with MIST**, according to Fig. 4e. A blanket claim that it outperforms every comparator would obscure this result. [Fig. 4e](https://www.nature.com/articles/s41587-025-02663-3#Fig4).
- **Fluorine detection:** The authors report **0.91 precision at 0.57 recall** on their in-house test library of **17,052 protonated Orbitrap spectra**, including 3,900 spectra of fluorinated molecules. SIRIUS did not exceed 0.51 precision under their evaluated settings. This is a specific benchmark result, not a universal accuracy estimate or proof of PFAS identification. [Results; Methods, “Fluorine detection”](https://doi.org/10.1038/s41587-025-02663-3).
- **Data quality:** In the reported ablations, GeMS-A10 outperformed larger, lower-quality training subsets. More spectra did not automatically yield better representations. [Methods; Extended Data Fig. 3a](https://doi.org/10.1038/s41587-025-02663-3).

## Boundaries of the evidence

The training data were restricted to positive ionization and singly charged precursors; 97% of the highest-quality GeMS-A spectra came from Orbitrap instruments. The model omits MS1 isotopic patterns and adduct distributions. These constraints matter when considering transfer to other acquisition settings. [Results; Methods; Discussion](https://doi.org/10.1038/s41587-025-02663-3).

The authors use Murcko-histogram-disjoint training/validation splits to reduce leakage from closely related structures. That safeguard strengthens evaluation but does not establish performance in every external laboratory or sample matrix. Atlas annotations remain predictions; network proximity does not establish molecular identity. [Methods; Extended Data Figs. 1 and 2](https://doi.org/10.1038/s41587-025-02663-3).

## From model capability to an MS workflow

A spectrum embedding describes fragmentation information. A chromatographic feature also has retention time, an abundance estimate and a relationship to a sample; a biological conclusion additionally requires a study design. These are separate levels of evidence. DreaMS addresses representation and annotation tasks, not the entire chain from raw acquisition to biological causality.

The proposed architecture in this repository therefore retains separate objects for spectra, features, candidate identities and biological claims. DreaMS outputs enter as model evidence; they cannot silently become confirmed identities or quantitative biomarker results. See [agent architecture](AGENT_ARCHITECTURE.md) and [evaluation plan](EVALUATION.md).

## Da Vinci's Poet commentary

The useful research direction is to make unannotated spectra more informative for prioritizing follow-up experiments. For a future metabolomics workflow, I would evaluate transfer on independent samples, track acquisition conditions and annotation confidence, and retain experimental confirmation for identity claims. These are proposed next steps, not experiments performed in this repository or clinical findings established by this paper.

## Credit and reproducibility resources

The model, datasets, benchmarks and experimental work belong to Bushuiev and colleagues. This post is independent commentary and implies no affiliation or endorsement.

- [Authors' DreaMS code](https://github.com/pluskal-lab/DreaMS)
- [Documentation and tutorials](https://dreams-docs.readthedocs.io/en/latest/)
- [Archived code release cited by the paper](https://zenodo.org/records/13843034)
- [Paper's data and code availability statements](https://doi.org/10.1038/s41587-025-02663-3), including GeMS, Atlas and model-weight resources. Full reproduction also depends on NIST20, which the authors identify as subject to licensing restrictions.

*Expanded for the Machine Learning in Computational Biology repository. Prepared from the supplied full-text paper; publication metadata cross-checked against the publisher listing. No model execution, benchmark replication or new data analysis was performed.*
