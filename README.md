# FedRecover

This repository publishes the corrected DynaFront reproducibility package for the revised FEMNIST results.

[Download the verified v1.0.0 archive](https://github.com/prsinha1/FedRecover/releases/download/v1.0.0/FedRecover_DynaFront_reproducibility_v1.0.0.zip)

[View the release record and validation notes](https://github.com/prsinha1/FedRecover/releases/tag/v1.0.0)

## FEMNIST correction

All four FEMNIST methods use 800 communication rounds. Bootstrap uses safety cap 800. The paper initialization, float32 arithmetic, learning rate 0.003, one local SGD step and other native controller settings are unchanged.

The complete grid contains 144 configurations: Greedy 18, FedCAM 18, Autonomous EXP3 54 and Bootstrap EXP3 54. The archive contains the source tree, pinned requirements, tests, expected results, compact evidence and paper figures.

## Quick start

After downloading and extracting the release archive, run:

```bash
python -m pip install torch==2.12.0 --index-url https://download.pytorch.org/whl/cpu
python -m pip install -r reproducibility_package/requirements-lock.txt
python -m pytest reproducibility_package/tests femnist800/test_matched.py
python reproduce_femnist800.py --output outputs/femnist800 --jobs 1
```

The archive README gives the full workflow, resource requirements, expected outputs and paper panel definitions.

## Validation

1. All 132 supplied tests passed in the pinned Python 3.12.14 environment.
2. The internal SHA256 manifest passed.
3. The outer release file index passed.
4. A clean extraction passed the same checks.
5. The Linear random array regeneration test permits at most one ULP across platforms, while the archived arrays retain exact SHA256 checks.

## Archive checksum

```text
fa62ae6835251deebdc3f85c6b2e1630d4971f2052a03322b5e44405ed74bc4e  FedRecover_DynaFront_reproducibility_v1.0.0.zip
```
