# Evaluation plan

**Proposed experiments only. No results have been generated.**

## Evaluate the model and the agent workflow separately

A better report is not evidence of a better molecular identification model. Assess three layers: analytical data quality, annotation performance and orchestration/reporting reliability.

| Layer | Comparison | Measures |
| --- | --- | --- |
| Analytical processing | Fixed processing workflow on reference mixtures, blanks and replicate QCs | Missingness, blank contamination, replicate variability, drift and spectrum–feature linkage errors |
| Annotation | Conventional similarity search, base representations and task-appropriate fine-tuned DreaMS using identical candidates and splits | Top-k retrieval, precision–recall, performance at abstention thresholds, coverage and unresolved-isomer rate |
| Domain transfer | Held-out instrument, laboratory, batch and sample matrix | Stratified performance and uncertainty; proportion excluded as unsupported |
| Orchestration | Same numerical tools in a deterministic pipeline versus the proposed assisted workflow | Task failures, provenance completeness, unsupported claims, review corrections, runtime and cost |
| Reporting | Blinded expert review of reports from fixed evidence packets | Citation support, numerical fidelity, uncertainty preservation and review time |

## Leakage and denominators

Split by molecular identity and structural similarity for model evaluation; where possible hold out studies or acquisition batches as well. Keep all replicate spectra of a held-out molecule together. Document known and unknown overlap with upstream pre-training data. Do not claim a fully unseen evaluation when overlap cannot be assessed.

Tune thresholds and calibration only on training/validation data. Freeze the candidate database, model, processing settings and evaluation protocol before final testing. Include absent-from-library cases to evaluate abstention. Report spectra, unique molecules and samples separately; repeated spectra are not independent biological replicates.

Use uncertainty intervals that respect the sampling unit, such as molecule-level resampling for spectral identification and participant-level resampling for biological outcomes. Report both eligible-input performance and the fraction of all inputs excluded.

## Proposed acceptance gates

Before integration, define numeric performance targets for the intended dataset and use case. No universal identification threshold is asserted here.

Minimum workflow gates: all published numeric claims trace to artifacts; every source file and model has recorded provenance; unsupported-domain inputs are blocked or explicitly routed; failed tasks cannot appear as successful analyses; no unreviewed claim becomes confirmed identity.

Test missing polarity, invalid arrays, duplicate scans, ambiguous precursors, absent blanks, conflicting candidate rankings, unavailable tools and misleading retrieved text. Verify both the stop condition and the retained audit record.

A multi-agent design earns adoption only if it improves a predefined outcome over the deterministic workflow while preserving scientific validity. Otherwise retain the simpler workflow.

## Reproducibility package

For each future experiment release the permitted input accessions, split IDs, code revision, environment, model hash, configurations, random seeds, exclusions, raw predictions, analysis scripts and evaluation report. Respect NIST and other library redistribution restrictions. Report unsuccessful experiments alongside improvements.

The [DreaMS paper](https://doi.org/10.1038/s41587-025-02663-3) motivates this evaluation; its published metrics are not acceptance results for this repository.
