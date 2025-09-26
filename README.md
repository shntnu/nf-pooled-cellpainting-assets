# nf-pooled-cellpainting-assets

Supporting assets and resources for the pooled Cell Painting Nextflow pipeline.

## Component

- **pcpip/** - Containerized PCPIP demo workflow (see [pcpip/README.md](pcpip/README.md))

## Related Repositories

- [nf-pooled-cellpainting](https://github.com/sequera-services/nf-pooled-cellpainting) - Main nf-core pipeline
- [nf-pooled-cellpainting-infra](https://github.com/broadinstitute/nf-pooled-cellpainting-infra) - AWS CDK infrastructure

## LoadData CSV generation and validation

We now provide a script to generate LoadData CSVs for the pipeline. The generator produces the canonical LoadData format used by the PCPIP workflow and is intended to be the primary source for building Run inputs going forward.

- Location: scripts/generate_loaddata_csvs.py (or scripts/generate_loaddata_csvs.sh depending on language)
- Purpose: produce LoadData CSVs from raw metadata and file layout.
- Validation: After generating LoadData CSVs, we validate the generated CSVs against the reference CSVs (where present) to build confidence that the generator is producing correct files. The validation step is intended to catch regressions and ensure the generator remains the source of truth.

(If you have a custom LoadData generator or workflow, ensure you follow the same validation approach to avoid divergence.)

## Filtering: consistency requirement

There is a filtering utility used to drop unwanted rows from LoadData CSVs: scripts/filter_loaddata_csvs_inplace.py.

Important:
- Filters used with filter_loaddata_csvs_inplace.py must be consistent with the filters applied inside run_pcpip.sh (or any wrapper script you use to run PCPIP).
- If the filters do not match between the generator step and the run script, the pipeline may include or exclude rows unexpectedly, which can cause downstream failures, mismatched QC results, or incorrect experiment grouping.
- When adjusting filters, update both the generator and run scripts (or centralize filters in a single config file) and re-run validation.

## Scripts directory structure (README examples)

The README shows an example scripts/ layout to help users navigate what’s provided. This is an explanatory example for README documentation and does not imply output paths produced by the pipeline.

Typical scripts/ contents:
- scripts/generate_loaddata_csvs.py
- scripts/filter_loaddata_csvs_inplace.py
- scripts/run_pcpip.sh
- scripts/qc_*.py or scripts/qc_*.ipynb

If you rely on example paths in the README, confirm them against your local copy of the scripts/ directory — the README documents script roles and examples, not the runtime output directories.

## Quality Control (QC)

- Removed outdated commentary regarding illumination correction phrasing that previously described functions as "vaguely circular and vaguely smooth." That language was outdated and has been removed.
- QC flags and checks are maintained in scripts/qc_*.py and the PCPIP notebooks. We recommend reviewing the QC flags periodically; if any flags are deprecated or added, update corresponding documentation and the README.
- If you discover QC flags that are out of date with current pipeline behavior, please file an issue and link it to this repository so documentation and code can be kept in sync.

## Contributing

If you update scripts or QC flags, please:
1. Update the README and relevant scripts.
2. Re-run the LoadData generator and validation.
3. Add/update tests or notebooks demonstrating the expected behavior.
4. Open a PR describing the change and include validation outputs where relevant.
