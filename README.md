# FedRecover: The Dynamics of Collapse and Recovery in Clustered Federated Learning

This repository publishes the code and reproducibility package for the paper *The Dynamics of Collapse and Recovery in Clustered Federated Learning*.

The package covers four experimental settings:

1. Linear regression
2. Gaussian mixture models
3. FEMNIST
4. California Housing

It contains scientific source snapshots, compact evidence, expected results, plotting code, integrity manifests, bounded smoke runs, and complete experiment runners.

## Download

[Download the verified v1.0.0 reproducibility archive](https://github.com/prsinha1/FedRecover/releases/download/v1.0.0/FedRecover_reproducibility_v1.0.0.zip)

[View the versioned release and validation record](https://github.com/prsinha1/FedRecover/releases/tag/v1.0.0)

## Environment

Use Python 3.12.14 and create the virtual environment outside the extracted archive. From the extracted archive root:

```bash
python -m pip install torch==2.12.0 --index-url https://download.pytorch.org/whl/cpu
python -m pip install -r reproducibility_package/requirements-lock.txt
```

## Run all tests

```bash
python -m pytest reproducibility_package/tests femnist800/test_matched.py
```

This command tests the Linear, GMM, FEMNIST, and Housing modules, as well as the revised 800 communication round FEMNIST pipeline.

## Verify the bundled evidence

Verification recomputes the reported tables from compact evidence without training models:

```bash
cd reproducibility_package
python reproduce.py --dataset all --mode verify --output-dir outputs/all_verify
cd ..
```

Dataset specific verification commands are documented in `reproducibility_package/README.md` inside the archive.

## Regenerate figures

The shared runner regenerates the archived Linear, GMM, FEMNIST, and Housing figures:

```bash
cd reproducibility_package
python reproduce.py --dataset all --mode figures --output-dir outputs/all_figures
cd ..
```

The revised arXiv FEMNIST panels use the separate 800 round workflow:

```bash
python reproduce_femnist800.py --output evidence_femnist800 --plot-only
```

## Run bounded smoke checks

```bash
cd reproducibility_package
python reproduce.py --dataset linear --mode smoke --output-dir outputs/linear_smoke
python reproduce.py --dataset gmm --mode smoke --output-dir outputs/gmm_smoke
python reproduce.py --dataset femnist --mode smoke --output-dir outputs/femnist_smoke
python reproduce.py --dataset housing --mode smoke --output-dir outputs/housing_smoke
cd ..
```

These execute the real scientific paths for a small number of rounds. They are environment and call path checks, not complete reproductions.

## Run complete experiments

Linear, GMM, and Housing use the common runner:

```bash
cd reproducibility_package
python reproduce.py --dataset linear --mode full --jobs 4 --output-dir outputs/linear_full
python reproduce.py --dataset gmm --mode full --jobs 4 --output-dir outputs/gmm_full
python reproduce.py --dataset housing --mode full --jobs 1 --output-dir outputs/housing_full
cd ..
```

The revised arXiv FEMNIST experiment uses 800 communication rounds for every method and safety cap 800 for Bootstrap:

```bash
python reproduce_femnist800.py --output outputs/femnist800 --jobs 1
```

The common package retains the earlier 200 round FEMNIST workflow for provenance and reviewer checks. It must not be substituted for the revised 800 round FEMNIST result.

## Validation

The release was tested from a clean extraction under Python 3.12.14 with the pinned dependencies.

1. All 132 supplied tests passed.
2. The internal SHA256 manifest passed.
3. The outer release file index passed.
4. The ZIP structure, permissions, timestamps, ordering, and CRC check passed.
5. A second deterministic build produced the same archive hash.
6. GitHub's displayed asset digest is checked against the local archive after upload.

## Archive checksum

```text
06de50e74434c505847ca2a22d097586e54036bd64c53bef6cd2a10f1694bc19  FedRecover_reproducibility_v1.0.0.zip
```

The archive contains the detailed reviewer guide, dataset specific commands, scientific endpoint definitions, provenance records, expected results, and paper figure definitions.
