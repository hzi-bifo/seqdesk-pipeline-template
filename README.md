# seqdesk-pipeline-template

A starting point for contributing a pipeline to [SeqDesk](https://seqdesk.org).
Clone or "Use this template", rename the placeholders, drop in your Nextflow
pipeline configuration, and open a pull request against the SeqDesk app repo.

> Full guide: https://seqdesk.org/docs/pipelines/adding-pipelines

## What is a SeqDesk pipeline package?

A SeqDesk pipeline package is a directory (`pipelines/<id>/`) of declarative
metadata that tells SeqDesk how to run a **Nextflow** workflow against the
samples in an order: which inputs it consumes, how to build the samplesheet, how
to invoke Nextflow, and how to discover and store the outputs. SeqDesk's generic
executor reads these files — you do not write app code.

## File layout

```
my-pipeline/
  manifest.json      # Source of truth: package metadata, inputs, execution, outputs
  definition.json    # DAG steps + process matchers for UI/trace visualisation
  registry.json      # UI configuration, config schema, visibility settings
  samplesheet.yaml   # Declarative rules for generating the pipeline samplesheet
  README.md          # Describe your pipeline (what it does, inputs, outputs)
  LICENSE            # Your chosen license (REQUIRED)
  parsers/           # Optional: output-file parser definitions (TSV/CSV/JSON)
    my-parser.yaml
  scripts/           # Optional: helper scripts (e.g. discover-outputs.mjs)
  test-data/         # REQUIRED: tiny dummy fixture so CI can run on dummy data
    README.md
    samples.csv
    reads/
      sample1_R1.fastq
      sample1_R2.fastq
```

The package `id` is the directory name and **must** match `manifest.package.id`.

### What each file does

- **manifest.json** — the source of truth for execution. Declares `package`
  metadata, the `files` map, `inputs` (what data the pipeline needs), `execution`
  (Nextflow pipeline name/version, profiles, `defaultParams`, `paramMap`,
  `paramRules`), and `outputs` (glob `discovery` patterns + where each artifact is
  stored). `paramMap` translates UI config keys into Nextflow flags; `paramRules`
  expresses conditional flags.
- **definition.json** — the pipeline DAG: `steps` with `dependsOn` and
  `processMatchers` (used to map Nextflow trace events to progress), plus
  `parameterGroups` describing user-tunable parameters.
- **registry.json** — UI-facing config: `configSchema`, `defaultConfig`,
  `visibility`, supported input scopes, and the displayed `icon`.
- **samplesheet.yaml** — declarative mapping from SeqDesk data (samples, reads,
  order metadata) to the CSV samplesheet rows/columns your Nextflow pipeline
  expects.
- **parsers/** — optional rules to extract metadata from output files; parsed
  values can be wired into `outputs[].parsed`.
- **test-data/** — a minimal dummy fixture (see below).

## Requirements

A contributed pipeline must satisfy all of the following:

1. **Nextflow only.** `execution.type` must be `"nextflow"` — it is the only
   supported engine.
2. **Ships a minimal `test-data/` fixture.** The package must include a tiny
   dummy fixture so CI can run the pipeline end-to-end on dummy data. See
   [`test-data/README.md`](test-data/README.md). Keep it small — no real data,
   no large reference databases.
3. **Declares a license.** Include a `LICENSE` file. This repo ships a clearly
   marked **placeholder** MIT license — **you must set the real license**:
   replace `<YEAR>` and `<COPYRIGHT HOLDER>` in [`LICENSE`](LICENSE), or replace
   the whole file with the license of your choice.

## How to use this template

1. Rename the directory to your pipeline `id` (e.g. `my-pipeline`), lowercase and
   hyphenated. Avoid a leading underscore — packages whose names start with `_`
   are ignored by the loader.
2. Replace every `my-pipeline` / `My Pipeline` / `my-org` / "TODO" placeholder in
   `manifest.json`, `definition.json`, `registry.json`, `samplesheet.yaml`, and
   `parsers/my-parser.yaml` with your real values.
3. Point `execution.pipeline` / `definition.url` at your Nextflow workflow.
4. Adjust `samplesheet.yaml` columns to match what your workflow expects.
5. Replace the dummy reads in `test-data/` with the smallest input that makes your
   pipeline succeed, and update `test-data/samples.csv` accordingly.
6. Set your real `LICENSE`.
7. Validate locally (from a SeqDesk app checkout with your package copied into
   `pipelines/<id>/`):

   ```bash
   npm run pipeline:validate
   ```

   The loader checks manifest schema compliance, that the folder name matches
   `manifest.package.id`, that all referenced files exist, that parser IDs used in
   outputs are defined, that definition/registry IDs match the manifest, that step
   dependencies and `fromStep` values resolve, and that runnable steps declare
   process matchers.

## Submission checklist

Before opening your pull request to the SeqDesk app repo (adds
`pipelines/<id>/`):

- [ ] Directory renamed to your `id`; no leading underscore.
- [ ] `manifest.package.id` matches the directory name.
- [ ] All `my-pipeline` / `my-org` / TODO placeholders replaced.
- [ ] `execution.type` is `"nextflow"`.
- [ ] `manifest.json`, `definition.json`, `registry.json` are valid JSON.
- [ ] `samplesheet.yaml` and any `parsers/*.yaml` are valid YAML and match your
      pipeline's expectations.
- [ ] `test-data/` ships a tiny dummy fixture (no real or large data) and CI can
      run the pipeline on it.
- [ ] A real `LICENSE` is declared (placeholder replaced).
- [ ] `README.md` describes the pipeline (purpose, inputs, outputs).
- [ ] `npm run pipeline:validate` passes.

See https://seqdesk.org/docs/pipelines/adding-pipelines for the complete,
up-to-date guide.

## License

This template ships a placeholder MIT `LICENSE` for convenience. Replace it with
your own before submitting — contributed pipelines must declare a real license.
