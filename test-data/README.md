# Test Data Fixture

Every contributed SeqDesk pipeline package **must** ship a minimal dummy test-data
fixture so that CI can run the pipeline end-to-end on dummy data without needing
real samples or large reference databases.

Keep this fixture as small as possible. It exists to exercise the pipeline's
wiring (samplesheet generation, Nextflow invocation, output discovery), not to
produce biologically meaningful results.

## Contents

```
test-data/
  README.md            # This file
  samples.csv          # Tiny sample manifest used by CI to drive the pipeline
  reads/
    sample1_R1.fastq   # Dummy paired-end reads (R1)
    sample1_R2.fastq   # Dummy paired-end reads (R2)
```

## Convention

- `samples.csv` lists one row per sample. Columns mirror the samplesheet your
  pipeline expects (see `../samplesheet.yaml`). Paths are **relative to this
  `test-data/` directory**.
- `reads/` holds the smallest valid FASTQ files that still let the pipeline run.
  The provided files contain only a handful of synthetic reads with uniform
  quality (`I`). Replace them with the smallest realistic input your pipeline
  needs (you may gzip them as `.fastq.gz`).
- Do **not** commit large files, real patient data, or reference databases here.
  If your pipeline needs a reference, stub or download a tiny dummy one inside the
  workflow instead.

## Customising for your pipeline

1. Update `samples.csv` columns to match `samplesheet.yaml`.
2. Replace the dummy reads (or add more samples) with the minimal input that makes
   your pipeline succeed.
3. Confirm CI can locate these files via the paths declared in `samples.csv`.
