# BB84 Predictive Channel Monitoring

### Stochastic Channel Modelling, QBER Forecasting and Anomaly Detection in Quantum Key Distribution

A simulation-based study of predictive monitoring for a time-varying BB84 Quantum Key Distribution (QKD) channel.

Instead of relying exclusively on a fixed Quantum Bit Error Rate (QBER) threshold, this project investigates whether learning the temporal behaviour of a quantum communication channel can help predict future QBER values and identify unusual deviations that may indicate interference.

The central idea is to **learn the channel's temporal behaviour, predict what it should produce next, and investigate unexpected deviations from that prediction.**

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

```bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
cd BB84-Predictive-Channel-Monitoring
```

Alternatively, download the repository using GitHub's **Code → Download ZIP** option and extract it.

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

- Step attack: sudden introduction of an attack.
- Ramp attack: gradual increase in attack intensity.

The objective is to investigate whether predictive monitoring can identify changes that may remain below conventional fixed QBER thresholds.

### 5.6 Anomaly Detection

Compares multiple monitoring approaches:

- Static QBER threshold.
- Moving-average monitoring.
- Innovation-based CUSUM.
- Anomaly-gated quarantine logic.
- Joint QBER and detection-yield monitoring using Mahalanobis distance.

### 5.7 Monte Carlo Evaluation

Evaluates monitoring performance under different simulated attack strengths and sifted block sizes.

The analysis investigates detection probability and detection delay across repeated simulation trials.

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

## 10. References and Acknowledgements

The project builds upon concepts from quantum key distribution, statistical forecasting and sequential anomaly detection.

Relevant foundational references include:

1. C. H. Bennett and G. Brassard, "Quantum cryptography: Public key distribution and coin tossing," 1984.
2. P. W. Shor and J. Preskill, "Simple Proof of Security of the BB84 Quantum Key Distribution Protocol," Physical Review Letters, 2000.
3. N. Gisin et al., "Quantum Cryptography," Reviews of Modern Physics, 2002.
4. E. S. Page, "Continuous Inspection Schemes," Biometrika, 1954.
5. F. X. Diebold and R. S. Mariano, "Comparing Predictive Accuracy," Journal of Business & Economic Statistics, 1995.
6. H. R. Künsch, "The Jackknife and the Bootstrap for General Stationary Observations," Annals of Statistics, 1989.

**AI assistance:** Disclose any AI tools used during implementation, debugging, statistical analysis and documentation according to the applicable institutional or publication guidelines.
