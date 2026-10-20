# FedRecover: The Dynamics of Collapse and Recovery in Clustered Federated Learning

This repository contains the code, tests, compact evidence, plotting pipeline, and full experiment runners for the Linear, GMM, FEMNIST, and Housing studies in the paper *The Dynamics of Collapse and Recovery in Clustered Federated Learning*.

## Download

[Download the verified v1.0.1 reproducibility archive](https://github.com/prsinha1/FedRecover/releases/download/v1.0.1/FedRecover_reproducibility_v1.0.1.zip)

[View the versioned release and validation record](https://github.com/prsinha1/FedRecover/releases/tag/v1.0.1)

SHA256:

`cffce91871ff0ed2abef633d8b7cf60efee80eef53873dfe22b5d5a7c28c1e57  FedRecover_reproducibility_v1.0.1.zip`

The release has two connected execution layers:

1. `reproducibility_package/` provides the shared reviewer interface for Linear, GMM, FEMNIST, and Housing. It supports evidence verification, figure generation, bounded smoke runs, and full experiments.
2. `reproduce_femnist800.py`, `femnist800/`, `reference_femnist800/`, and `evidence_femnist800/` provide the 800 communication round FEMNIST experiment used for the paper figures.

Detailed instructions for all four datasets are in `reproducibility_package/README.md`. A step by step reviewer workflow is in `reproducibility_package/REVIEWER_GUIDE.md`.

## Environment

Use Python 3.12.14 and create the virtual environment outside the extracted archive. The validated reference environment used Windows CPU and PyTorch 2.12.0 CPU.

From the archive root:

```bash
python -m pip install torch==2.12.0 --index-url https://download.pytorch.org/whl/cpu
python -m pip install -r reproducibility_package/requirements-lock.txt
```

## Run all supplied tests

From the archive root:

```bash
python -m pytest reproducibility_package/tests femnist800/test_matched.py
```

This command tests the Linear, GMM, FEMNIST, and Housing modules, their scientific endpoints, evidence manifests, figure contracts, smoke paths, full experiment manifests, output safety, and the 800 round FEMNIST pipeline.

The archived Linear initialization arrays retain exact SHA256 checks. Their seeded regeneration is compared with a tolerance of at most one unit in the last place because NumPy does not promise identical generated floating point values across platforms.

## Verify the bundled evidence for all four datasets

Verification recomputes the reported tables from the compact evidence without training models:

```bash
cd reproducibility_package
python reproduce.py --dataset linear --mode verify --output-dir outputs/linear_verify
python reproduce.py --dataset gmm --mode verify --output-dir outputs/gmm_verify
python reproduce.py --dataset femnist --mode verify --output-dir outputs/femnist_verify
python reproduce.py --dataset housing --mode verify --output-dir outputs/housing_verify
python reproduce.py --dataset all --mode verify --output-dir outputs/all_verify
cd ..
```

Each verification report should record `PASS`. The complete training status is intentionally `NOT_RUN` in this mode.

## Regenerate figures from bundled evidence

The shared runner regenerates the archived figures for each dataset:

```bash
cd reproducibility_package
python reproduce.py --dataset linear --mode figures --output-dir outputs/linear_figures
python reproduce.py --dataset gmm --mode figures --output-dir outputs/gmm_figures
python reproduce.py --dataset femnist --mode figures --output-dir outputs/femnist_figures
python reproduce.py --dataset housing --mode figures --output-dir outputs/housing_figures
cd ..
```

For the 800 round FEMNIST panels in the paper, use the separate command described below. The FEMNIST figure command above belongs to the common 200 round workflow.

## Run bounded scientific smoke checks

These commands execute the real algorithm paths for a small number of rounds. They check the environment and scientific call paths but do not reproduce the full populations:

```bash
cd reproducibility_package
python reproduce.py --dataset linear --mode smoke --output-dir outputs/linear_smoke
python reproduce.py --dataset gmm --mode smoke --output-dir outputs/gmm_smoke
python reproduce.py --dataset femnist --mode smoke --output-dir outputs/femnist_smoke
python reproduce.py --dataset housing --mode smoke --output-dir outputs/housing_smoke
cd ..
```

## Inspect the complete experiment plans

The following commands write the declared task matrices without launching training:

```bash
cd reproducibility_package
python reproduce.py --dataset linear --mode full --dry-run --output-dir outputs/linear_full_preflight
python reproduce.py --dataset gmm --mode full --dry-run --output-dir outputs/gmm_full_preflight
python reproduce.py --dataset femnist --mode full --dry-run --output-dir outputs/femnist_full_preflight
python reproduce.py --dataset housing --mode full --dry-run --output-dir outputs/housing_full_preflight
cd ..
```

## Run the complete Linear, GMM, and Housing experiments

```bash
cd reproducibility_package
python reproduce.py --dataset linear --mode full --jobs 4 --output-dir outputs/linear_full
python reproduce.py --dataset gmm --mode full --jobs 4 --output-dir outputs/gmm_full
python reproduce.py --dataset housing --mode full --jobs 1 --output-dir outputs/housing_full
cd ..
```

These experiments are computationally expensive. Repeating an identical command resumes checksum verified completed cells. The generated `REPRODUCTION_REPORT.json` distinguishes successful execution from numerical agreement with the packaged reference.

The common package also retains the 200 round FEMNIST workflow for provenance and reviewer checks. The paper's 800 round FEMNIST result is generated with `reproduce_femnist800.py`.

## Run the 800 round FEMNIST experiment

From the archive root, using a short output path on Windows:

```bash
python reproduce_femnist800.py --output outputs/femnist800 --jobs 1
```

All four FEMNIST methods use 800 communication rounds. Synchronized entry uses $H_{\max}=800$. The paper initialization, float32 arithmetic, learning rate 0.003, one local SGD step, and other controller settings are fixed across the declared grid. The grid contains 144 configurations: 18 Greedy, 18 FedCAM, 54 autonomous EXP3, and 54 synchronized entry configurations.

The runner freshly trains all 144 configurations, records each client update independently, audits aggregation and continuity, checks sampled native gradients, evaluates exact checkpoints, draws the paper panels from generated outputs, and compares the numerical plot sources with the packaged reference. Reference tables are used only after training for validation. Routing, assignment, and recovery quantities must match exactly. Continuous losses and accuracies use absolute tolerance `1e-6` and relative tolerance `1e-7`. A mismatch returns a nonzero exit code and retains the fresh outputs.

Repeat the same command to resume completed cells after checksum verification. Source or configuration changes are rejected. Use a new output directory for an independent repeat.

Runtime and disk requirements are substantial. Raw client arrays are generated locally and are intentionally excluded from the compact release archive. Each worker uses one CPU thread. FedCAM retains several GiB of client updates and allocates another 3.03 GiB array when closing an 800 round run. Six simultaneous workers exhausted memory on the 32 GiB validation machine, so reduce concurrency rather than changing the optimizer or initialization.

Fixed benchmark data and pretrained paper initialization vectors are bundled inputs. Fresh means new federated training trajectories, not regenerated benchmark examples or repeated initialization pretraining.

Independent client updates are enforced by `reproducibility_package/src/femnist/update_audit.py` and its owned model constructor. Every local training call receives a private copy of the common starting model. Saved client rows encode the actual outputs losslessly against that round's shared snapshot. FedCAM records separate global and cluster outputs.

## Plot the supplied 800 round FEMNIST evidence

```bash
python reproduce_femnist800.py --output evidence_femnist800 --plot-only
```

This command evaluates retained models and plots the supplied evidence. It does not train. Every cell retains checkpoints at $W^{200}$, $W^{400}$, and $W^{800}$, along with logs and audit receipts. The 18 shared starting matrices are stored once under `initializations/`. Redundant final model files are omitted because checkpoint 800 contains the same state, including the FedCAM global and cluster predictors.

The paper PNG and PDF panels and the assembled Figure 1 are in `evidence_femnist800/figures/paper/`.

## FEMNIST panel definitions

Panel (e) uses held out evaluation at the exact terminal state $W^{800}$ for every method. Panels (f), (g), and (h) use native preupdate trajectories $W^0,\ldots,W^{799}$. Appendix B.2 uses the actual routed test loss in the last logged state $W^{799}$. The representative trajectories use corruption level 0.60, data seed 1, and selector seed 0. FedCAM always uses its additive global plus cluster logits.

Uniform exact probes at $W^{200}$, $W^{400}$, and $W^{800}$ are supplied as additional tables. Client assignment is distinct from classification accuracy.

There are three distinct data and initialization contexts for each corruption level. The EXP3 methods additionally repeat each context with three selector seeds. Fractions across nine controller cells at a fixed corruption level therefore describe the declared controller grid, not nine independent dataset realizations.

## Measured exact FEMNIST outcomes at $W^{800}$

| Method | Cells | Two clusters, train | Two clusters, test | Perfect, train | Perfect, test |
| --- | ---: | ---: | ---: | ---: | ---: |
| Autonomous EXP3 | 54 | 18 | 22 | 13 | 13 |
| Synchronized entry | 54 | 42 | 42 | 30 | 33 |
| FedCAM | 18 | 0 | 0 | 0 | 0 |
| Greedy | 18 | 0 | 0 | 0 | 0 |

All 144 initialization checks, every round route and identity linkage checks, saved aggregation audits, 2,304 sampled cluster gradient recomputations, and 288 FedCAM global gradient recomputations passed. Sampled prefix records also match the packaged 200 round outputs exactly. These checks do not claim 144 independent repeated 800 round sweeps. The fresh workflow supplies the means to perform and validate a new repeat.

## Integrity records

`reproducibility_package/SHA256_MANIFEST.csv` covers the common package. `RELEASE_FILE_INDEX.json` records the files in the outer release. The expected result manifests cover each dataset's compact evidence. The completed validation record is in `reproducibility_package/provenance/VALIDATION_STATUS.md`.
