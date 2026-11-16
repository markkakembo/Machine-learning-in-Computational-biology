# Proposed agent architecture for MS data

**Design specification; not implemented or experimentally validated.**

## What “agent” means here

An agent is a bounded workflow role with typed inputs, explicit tool permissions, a stopping condition and an auditable output. Most analytical roles should be deterministic workers. A language model is optional for planning and evidence-grounded prose; it is not the mass spectrometer, numerical inference engine or final scientific authority.

The design combines a deterministic workflow graph with limited agent assistance. It does not assume that adding more agents improves scientific performance.

## Roles and contracts

| Role | Inputs and tools | Required output | Stop or escalate when |
| --- | --- | --- | --- |
| Orchestrator | Study manifest; versioned workflow and tool registry | Task graph, configuration, run state and budget | Required metadata are absent; a task exceeds its resource budget |
| Ingestion and QC | Read-only source files; format parser; numerical QC tools | Checksums, acquisition metadata, QC report and exclusion reasons | Parsing fails, acquisition mode is unknown or quality gates fail |
| Feature processing | Validated LC–MS data; peak detection, alignment and integration tools | Feature table and explicit feature-to-MS/MS links | Fragment-to-precursor linkage is ambiguous or settings are incompatible |
| Representation | Eligible MS/MS spectra; pinned DreaMS preprocessing and checkpoint | Embeddings, input spectrum IDs, checkpoint hash and domain flags | Mode/charge is unsupported; preprocessing is invalid |
| Annotation | Embeddings; versioned reference libraries; baseline search tools | Ranked candidates, raw scores, candidate-set definition and evidence | No defensible match or competing identities remain unresolved |
| Evidence auditor | Candidate records, QC, provenance and retrieved primary sources | Unsupported-claim flags, conflicts and uncertainty statement | A claim lacks evidence or a citation does not support it |
| Biological interpretation | Reviewed annotations; feature abundances; study design | Qualified associations and alternative explanations | Identity, sample size or confounding prevents interpretation |
| Report composer | Approved structured evidence only | Human-readable report with record-level citations | A generated statement cannot be traced to an approved record |
| Researcher | Evidence report and original analytical outputs | Recorded approval, correction, rejection or further experiment | Experimental confirmation is required |

## MS-specific execution

1. **Register the acquisition.** Preserve original vendor files and checksums. Use validated conversion when needed; record converter version and settings. Retain polarity, charge, precursor mass, collision energy, instrument, retention-time units, acquisition mode and sample/batch IDs. Do not infer missing values from filenames without labeling the inference.
2. **Assess analytical quality.** Evaluate blanks, pooled QC, injection order, replicate variability, carryover and batch drift when those controls exist. Missing controls mean “not assessed,” not “passed.” Thresholds are study-specific and fixed before outcome inspection.
3. **Separate features from spectra.** Feature detection and abundance integration are separate from embedding generation. Preserve one-to-many and ambiguous feature–spectrum relationships. For DIA, require a validated deconvolution route before treating fragments as a single-precursor MS/MS spectrum.
4. **Check model eligibility.** Match the selected checkpoint's documented input domain and preprocessing. Unknown metadata go to review. Out-of-domain spectra are excluded from this route or processed by an explicitly evaluated alternative.
5. **Generate representations and rank candidates.** Store embedding and checkpoint provenance. Use an appropriate fine-tuned similarity model for its documented task; do not assume base embedding cosine similarity is interchangeable. Record mass tolerance, adduct assumptions, library version and candidate filters.
6. **Audit uncertainty.** Preserve isomer ambiguity, disagreeing tools and missing library coverage. Similarity scores are not identification probabilities. Any probability calibration or false-discovery estimate requires its own validated procedure.
7. **Interpret and review.** Link reviewed annotations to quantitative features and the study design. Keep identification uncertainty visible in pathway hypotheses. A reviewer can approve a qualified report without promoting tentative candidates to confirmed identities.

## State, permissions and recovery

Proposed task states are `pending → running → succeeded`, with `failed`, `held_for_review` and `excluded` recorded explicitly. A failure cannot produce a successful scientific result. An exclusion remains visible in run denominators.

Use an idempotency key derived from input hashes, tool/checkpoint versions and configuration. Store outputs immutably. Retry transient infrastructure errors at most twice; deterministic input errors go directly to review. Changing a parameter creates a new run, not a silent revision.

Analytical workers read registered inputs and write only their own output artifacts. The reporting role cannot change numeric results or acquisition records. Retrieved text is evidence, never executable instructions. External searches receive only approved, non-sensitive scientific queries. Publication is a separate researcher decision.

## Worked routing example — hypothetical

A spectrum has a high DreaMS similarity score to two candidate isomers. The annotation role retains both candidates and their library accessions. The auditor records unresolved identity and checks whether a reference standard was measured under comparable conditions. The report may describe a tentative annotation; it cannot claim the compound has been confirmed.

If polarity is missing, the representation task is held before inference. If blanks are absent, blank contamination remains unassessed. Neither case is “fixed” by a language-model guess.

## Relationship to DreaMS

DreaMS supplies a representation/annotation component, not this orchestration system. Consult the [paper review](DREAMS_REVIEW.md), [upstream code](https://github.com/pluskal-lab/DreaMS) and [documentation](https://dreams-docs.readthedocs.io/en/latest/) before implementation. Pin versions and follow the applicable model and dataset licenses.
