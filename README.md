# BB84 Predictive Channel Monitoring

### Stochastic Channel Modelling, QBER Forecasting and Anomaly Detection in Quantum Key Distribution

A simulation-based study of predictive monitoring for a time-varying BB84 Quantum Key Distribution (QKD) channel.

Instead of relying exclusively on a fixed Quantum Bit Error Rate (QBER) threshold, this project investigates whether learning the temporal behaviour of a quantum communication channel can help predict future QBER values and identify unusual deviations that may indicate interference.

The central idea is to:

**Learn the channel's temporal behaviour, predict what it should produce next, and investigate unexpected deviations from that prediction.**

> **Scope:** This is a numerical simulation and channel-monitoring experiment, not a security proof or a deployable QKD system. An anomaly alarm indicates that the channel should be investigated; it does not establish that a key is secure. Real QKD deployments require appropriate finite-key security analysis and protocol-level abort rules.

---

## 1. Project Structure

The complete implementation is maintained in a single Python script.

```text
BB84-Predictive-Channel-Monitoring/
│
├── README.md
├── bb84_channel_monitor.py
├── requirements.txt
├── .gitignore
│
└── plots/
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

The `plots/` directory contains example experimental outputs. Running the program regenerates these figures.

## 2. Requirements

- Python 3.9 or newer
- NumPy
- SciPy
- Matplotlib

No Qiskit installation, external dataset, GPU or physical quantum computer is required.

All channel data, noise processes and attack scenarios are simulated.

## 3. Installation and Execution

### Step 1: Clone the repository

Replace `OWNER` and `REPOSITORY` with the actual GitHub username and repository name.

```bash
git clone https://github.com/OWNER/REPOSITORY.git
cd REPOSITORY
```

Alternatively, download the repository using GitHub's **Code → Download ZIP** option and extract it.

Open a terminal inside the extracted project directory.

### Step 2: Create a virtual environment (recommended)

**Windows (PowerShell):**

```powershell
py -3 -m venv .venv
.venv\Scripts\Activate.ps1
```

If PowerShell blocks activation, use Command Prompt:

```cmd
.venv\Scripts\activate.bat
```

**Linux / macOS:**

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### Step 3: Install dependencies

```bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

### Step 4: Execute the program

```bash
python bb84_channel_monitor.py
```

The program executes the complete simulation pipeline, including:

- BB84 baseline simulation
- Stochastic channel modelling
- Temporal correlation analysis
- QBER forecasting
- Statistical validation
- Eavesdropping simulations
- Anomaly detection
- Monte Carlo robustness evaluation

Numerical results are printed in the terminal, and ten experimental figures are generated in the `plots/` directory.

Typical runtime is approximately one to two minutes on a modern laptop, depending on hardware and simulation settings.

Fixed random seeds are used to support reproducibility.

## 4. Running in Google Colab

The program can also be executed using Google Colab.

1. Open [Google Colab](https://colab.research.google.com/).
2. Upload `bb84_channel_monitor.py`.
3. Install the required dependencies:

```python
!pip install numpy scipy matplotlib
```

4. Execute the script:

```python
%run bb84_channel_monitor.py
```

The generated figures are saved in the `plots/` directory in the Colab environment.

## 5. Methodology and Implementation

The simulation is organized into the following stages.

### 5.1 BB84 Baseline Simulation

- Generates random quantum bits and measurement bases.
- Models quantum-state preparation and measurement.
- Introduces Pauli error channels.
- Performs basis reconciliation and key sifting.
- Calculates the resulting QBER.

### 5.2 Stochastic Channel Modelling

- Models time-varying Pauli noise using a logit autoregressive process.
- Introduces temporal dependence between successive channel blocks.
- Models finite-sample observations using binomial sampling.

### 5.3 Temporal Behaviour Analysis

- Studies the autocorrelation of the underlying channel.
- Examines the effect of finite-sample noise on observed temporal correlation.
- Compares underlying and observed channel behaviour.

### 5.4 Predictive Modelling

- Implements causal walk-forward forecasting.
- Compares previous-block, moving-average and autoregressive predictors.
- Evaluates prediction errors and residual behaviour.
- Uses bootstrap analysis and Diebold–Mariano statistical comparison to investigate forecasting performance.

### 5.5 Eavesdropping Simulation

Introduces partial intercept-resend attacks under two scenarios:

**Step attack:** Sudden introduction of an attack.

**Ramp attack:** Gradual increase in attack intensity.

The objective is to investigate whether predictive monitoring can identify changes that may remain below conventional fixed QBER thresholds.

### 5.6 Anomaly Detection

Compares multiple monitoring approaches:

- Static QBER threshold.
- Moving-average monitoring.
- Innovation-based CUSUM.
- Anomaly-gated state quarantine.
- Joint QBER and detection-yield monitoring using Mahalanobis distance.

The joint Mahalanobis detector evaluates QBER and detection-yield innovations together, accounting for their estimated correlation.

It is evaluated separately in the gradual-ramp attack experiment and is not included in the Monte Carlo detector comparison.

### 5.7 Monte Carlo Robustness Evaluation

The Monte Carlo experiment compares four monitoring methods:

1. Static QBER threshold.
2. Moving-average monitoring.
3. Innovation-based CUSUM.
4. CUSUM with anomaly-gated quarantine.

The main experiment evaluates seven partial intercept-resend attack strengths:

```text
f = 0.02, 0.04, 0.06, 0.08, 0.10, 0.12, 0.16
```

The detectors are calibrated on attack-free trajectories using a common target false-alarm rate of 0.01 per block, and evaluated on fresh simulated trajectories.

The main experiment uses:

- 150 calibration runs.
- 150 independent evaluation runs.

A separate block-size scaling experiment evaluates four sifted block sizes:

```text
500, 2000, 5000, 20000
```

This experiment uses 100 calibration runs and 100 evaluation runs for each block size, at an interception fraction of 0.10.

The evaluation investigates:

- Probability of detection within the attack window.
- Mean detection delay when an attack is detected.
- Held-out false-alarm behaviour.
- Effect of sifted block size on detection performance.

The joint QBER/yield Mahalanobis detector is evaluated separately in the ramp-attack experiment, not in this Monte Carlo comparison.

## 6. Execution and Validation

After running the program, verify the following:

- The Python process completes without an exception.
- Numerical simulation and forecasting results appear in the terminal.
- Step and ramp attack experiments execute.
- Monte Carlo evaluation completes.
- All ten PNG figures are generated in the `plots/` directory.

### Theoretical sanity check

For a Pauli error probability of:

\[
p=0.10
\]

the theoretical baseline QBER is:

\[
QBER=\frac{2p}{3}\approx 0.0667
\]

The simulated result should fluctuate around this value because of finite random sampling.

Additional checks:

- The simulated channel should exhibit temporal correlation.
- Forecasting methods should be compared against historical baseline predictors.
- Step and ramp attack detection results should be examined separately.
- Monte Carlo detection probabilities should be interpreted as finite-trial estimates.

**Testing note:** There is currently no separate `pytest` test suite. Running the complete script provides the end-to-end execution check.

## 7. Limitations

The following limitations apply to the current implementation:

- The channel, observations, noise and attacks are simulated; no experimental hardware measurements or real network datasets are used.
- The attack model represents partial intercept-resend behaviour and does not cover every possible eavesdropping strategy.
- Results depend on channel-drift parameters, block sizes, detector settings and random seeds.
- QBER-only monitoring cannot distinguish every malicious change from natural channel fluctuations.
- Monte Carlo experiments use a finite number of trials; small differences between detector scores should not be over-interpreted.
- The implementation does not include decoy states, finite-key security bounds, key distillation or a complete QKD protocol stack.
- Matplotlib may display a non-interactive-backend warning in headless environments. If execution completes and the figures are saved, this warning alone does not indicate failure.

## 8. Dependencies

The required Python packages are listed in `requirements.txt`.

```text
numpy>=1.20
scipy>=1.7
matplotlib>=3.3
```

## 9. Reproducibility

Fixed random seeds are used across simulation stages to support reproducibility.

Numerical results may change if simulation parameters, random seeds or library versions are modified.

All experimental figures are regenerated when the program is executed.

## 10. References

The project draws upon foundational research in quantum key distribution, quantum-channel modelling, statistical forecasting and anomaly detection.

### Foundational BB84 and Quantum Cryptography

1. C. H. Bennett and G. Brassard, "Quantum cryptography: Public key distribution and coin tossing," Proceedings of the IEEE International Conference on Computers, Systems and Signal Processing, 1984.

2. P. W. Shor and J. Preskill, "Simple Proof of Security of the BB84 Quantum Key Distribution Protocol," Physical Review Letters, vol. 85, p. 441, 2000.

3. N. Gisin, G. Ribordy, W. Tittel and H. Zbinden, "Quantum Cryptography," Reviews of Modern Physics, vol. 74, pp. 145–195, 2002.

### Statistical Forecasting and Monitoring

4. E. S. Page, "Continuous Inspection Schemes," Biometrika, vol. 41, pp. 100–115, 1954.

5. F. X. Diebold and R. S. Mariano, "Comparing Predictive Accuracy," Journal of Business & Economic Statistics, vol. 13, pp. 253–263, 1995.

6. H. R. Künsch, "The Jackknife and the Bootstrap for General Stationary Observations," The Annals of Statistics, vol. 17, pp. 1217–1241, 1989.

### Recent Research on Time-Varying Quantum Channels and QKD Anomaly Detection

7. J. Ur Rehman, H. Al-Hraishawi, T. Q. Duong, S. Chatzinotas and H. Shin, "On Estimating Time-Varying Pauli Noise," IEEE Transactions on Communications, vol. 72, no. 4, pp. 2079–2089, 2024.

   DOI: https://doi.org/10.1109/TCOMM.2023.3341886

8. J. Liu, B. Huang, J. Su, Q. Peng and A. Huang, "Deep Anomaly Detection for Active Attacks on the Receiver in Quantum Key Distribution," Optics Express, vol. 33, no. 22, pp. 47137–47150, 2025.

   DOI: https://doi.org/10.1364/OE.575434

9. S. P. Kish, C. Thapa, M. Sayat, H. Suzuki, J. Pieprzyk and S. Camtepe, "Mitigation of Channel Tampering Attacks in Continuous-Variable Quantum Key Distribution," Physical Review Research, vol. 6, 023301, 2024.

   DOI: https://doi.org/10.1103/PhysRevResearch.6.023301

These recent studies provide related context for time-varying quantum-channel estimation and QKD anomaly detection. They investigate different approaches and protocol settings; they should not be interpreted as validating the exact predictive monitoring framework implemented in this project.

## 11. Contributions and Acknowledgements

**Project Developer:** Krishna

**AI Development Assistance:** Claude (Anthropic)

Claude was used as an AI-assisted development collaborator throughout this project. Its contributions included assistance with Python implementation, code development, debugging, statistical methodology discussions, experimental analysis, interpretation of simulation results and technical documentation.

The project concept, research direction, novelty objectives, experimental decisions, implementation choices and final evaluation were directed and reviewed by the project developer.

AI-assisted outputs were subject to human review and verification. Claude is acknowledged transparently as an AI development tool and is not represented as a human author or independent researcher.

The project developer retains responsibility for the final implementation, experimental claims, interpretation and documentation.

---

**Project Status:** Simulation-based research and experimental evaluation.

**Implementation:** Python | NumPy | SciPy | Matplotlib

**Repository:** BB84 Predictive Channel Monitoring
