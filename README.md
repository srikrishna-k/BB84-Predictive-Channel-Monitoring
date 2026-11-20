# BB84 Predictive Channel Monitoring

A simulation study of predictive monitoring for a time-varying BB84 quantum key distribution (QKD) channel. Instead of relying only on a fixed QBER threshold, the program learns the channel's temporal behaviour, forecasts the next block's QBER, and examines unusually large prediction errors as possible signs of interference.

> **Scope:** This is a numerical simulation and monitoring experiment, not a security proof or a deployable QKD system. An alarm is an indication that the channel should be investigated; it does not establish that an encryption key is secure. Real QKD deployments still require an appropriate finite-key security analysis and protocol-level abort rules.

## 1. Project contents

```text
bb84_channel_monitor_fixed/
├── README.md                       # This guide
├── bb84_channel_monitor.py          # Complete program (single Python script)
├── requirements.txt                # Python dependencies
├── .gitignore
└── plots/                           # Ten output figures, regenerated on each run
    ├── 01_channel_noise_evolution.png
    ├── 02_expected_vs_observed_qber.png
    ├── 03_acf_underlying_channel.png
    ├── 04_correlation_attenuation_comparison.png
    ├── 05_walk_forward_actual_vs_predicted.png
    ├── 06_ar1_residual_acf.png
    ├── 07_phase1_validation_dashboard.png
    ├── 08_attack_innovation_monitoring_dashboard.png
    ├── 09_multivariate_ramp_attack_dashboard.png
    └── 10_robustness_and_roc_benchmark.png
```

The complete implementation is in **one file**, `bb84_channel_monitor.py`. The `plots/` directory contains example outputs; running the program regenerates them.

## 2. Requirements

- Python 3.9 or newer
- NumPy
- SciPy
- Matplotlib

No Qiskit installation, external dataset, quantum computer, or GPU is required. All channel data and attack scenarios are simulated.

## 3. Run locally

### Step 1 — Get the project

Download the repository using **Code → Download ZIP** on GitHub and extract it, or clone it:

```bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
cd bb84_channel_monitor_fixed
```

### Step 2 — (Recommended) Create a virtual environment

**Windows (PowerShell):**

```powershell
py -3 -m venv .venv
.venv\Scripts\Activate.ps1
```

If PowerShell blocks activation, you can use Command Prompt instead:

```bat
.venv\Scripts\activate.bat
```

**Linux / macOS:**

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### Step 3 — Install dependencies

Run this from the project folder:

```bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

### Step 4 — Execute the program

```bash
python bb84_channel_monitor.py
```

The script runs the baseline BB84 simulation, time-varying channel experiment, forecasting and statistical validation, attack-monitoring scenarios, and Monte Carlo evaluation. It prints numerical results in the terminal and saves/regenerates ten figures in the `plots/` folder.

Typical runtime is around one to two minutes on a modern laptop; actual time depends on the machine. The random seeds are fixed in the script to make the experiments reproducible.

## 4. Run in Google Colab

1. Open [Google Colab](https://colab.research.google.com/).
2. Upload `bb84_channel_monitor.py` to the Colab session, or paste its contents into a code cell.
3. In a cell, install the dependencies:

   ```python
   !pip install numpy scipy matplotlib
   ```

4. Run the uploaded script:

   ```python
   %run bb84_channel_monitor.py
   ```

The figures will be displayed where supported and saved under `plots/` in the Colab session. You can download the generated folder from the Colab Files panel.

## 5. What the program does

1. **BB84 baseline simulation:** prepares random bits and bases, applies Pauli errors, performs Bob's measurement, sifts the key, and calculates QBER.
2. **Stochastic channel model:** models time-varying Pauli noise using a logit autoregressive process.
3. **Finite-sample observations:** models the observed number of errors using binomial sampling, representing shot noise in a finite sifted block.
4. **Temporal analysis:** calculates autocorrelation and examines how finite-sample noise attenuates observed temporal correlation.
5. **Forecasting:** compares causal walk-forward predictors, including previous-block, moving-average, and autoregressive models.
6. **Statistical validation:** evaluates forecast errors using residual diagnostics, a moving-block bootstrap, and a Diebold–Mariano comparison.
7. **Attack simulation:** introduces partial intercept-resend attacks, including step and gradual-ramp scenarios.
8. **Detection methods:** compares a static QBER threshold, moving-average monitoring, innovation-based CUSUM, quarantine logic, and a joint QBER/yield monitor.
9. **Monte Carlo evaluation:** compares detection probability and delay across simulated attack strengths and sifted block sizes.

## 6. How to check that it ran correctly

After execution, check the following:

- The terminal reaches the final message: `ALL 10 PUBLICATION-GRADE PLOTS REGENERATED AND SAVED IN ./plots DIRECTORY`.
- The `plots/` directory contains all ten PNG files listed above.
- The terminal prints the baseline QBER, forecast comparison, statistical test results, step-attack and ramp-attack summaries, and Monte Carlo detection results.
- The process exits without a Python traceback.

Useful sanity checks from the model:

- For a Pauli error probability of `p = 0.10`, the theoretical baseline QBER is `2p/3 ≈ 0.0667`. A finite random simulation will fluctuate around this value.
- The simulated channel has temporal correlation, so forecast methods can be compared against a historical-mean baseline.
- In the ramp scenario, the program reports the static-threshold and joint QBER/yield monitor detections separately.
- Monte Carlo probabilities are estimates from a finite number of trials and therefore vary if the random seeds or experiment settings are changed.

There is currently no separate `pytest` test suite. Running the script from start to finish is the provided end-to-end execution check.

## 7. Important interpretation and limitations

- The channel, noise, observations, and attacks are simulated; there are no experimental hardware measurements or real network datasets.
- The attack model is a partial intercept-resend model and does not represent every possible eavesdropping strategy.
- Results depend on the selected channel-drift parameters, block size, detector settings, and random seeds.
- QBER-only monitoring cannot distinguish every malicious change from a natural channel fluctuation.
- The Monte Carlo experiment is finite; small differences between detector scores should not be over-interpreted.
- The model does not implement decoy states, finite-key security bounds, key distillation, or a complete QKD stack.
- Matplotlib may print a non-interactive-backend warning in headless terminals. If the run completes and the PNG files are saved, this warning does not by itself indicate a failed simulation.

## 8. Dependencies

The exact minimum package versions are listed in `requirements.txt`:

```text
numpy>=1.20
scipy>=1.7
matplotlib>=3.3
```

## 9. Reproducibility

The script sets fixed random seeds for its simulation stages. If you modify parameters, random seeds, or library versions, numerical results may change. The plots are regenerated when the script is run.

## 10. Citation and acknowledgement

If you use or extend this work, cite the relevant BB84 and statistical-monitoring literature. The code is an educational/research simulation; add the full bibliographic references used in your accompanying report or paper here before publication.

If AI tools contributed to the implementation, debugging, analysis, or documentation, disclose the tools and the nature of their contribution accurately according to your institution's or venue's rules.
