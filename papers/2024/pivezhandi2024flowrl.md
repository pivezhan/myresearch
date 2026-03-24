# FlowRL: Flow-Augmented Few-Shot Reinforcement Learning for Semi-Structured Sensor Data

**Citation Key**: `pivezhandi2024flowrl`

**Year**: 2024

**Type**: article

**Authors**: Pivezhandi, Mohammad and Saifullah, Abusayeed

**Venue**: arXiv preprint arXiv:2409.14178

---

## Key Points

- Flow-Augmented Reinforcement Learning (FlowRL): Novel method leveraging continuous normalizing flows (CNFs) to generate high-quality synthetic data for few-shot RL with semi-structured sensor data
- Up to 35% higher frame rates and faster Q-value convergence vs baselines (DynaQ, PlanGAN, MAML, zTT) on NVIDIA Jetson TX2 DVFS task
- Latent space bootstrapping for diversity: Resampling technique generates B bootstrapped samples {x^b_0}^B_b=1 to enhance synthetic trajectory diversity and mitigate overfitting
- Feature-weighted flow matching: Random Forest-based importance weighting (λ_i = Importance(x_t,i) / Σ_j Importance(x_t,j)) preserves critical data correlations (e.g., temperature-frequency relationships)
- Better correlation preservation: FlowRL-generated data captures feature correlations more accurately than model-based approaches (Pearson's correlation analysis)
- Lower Dynamic Time Warping (DTW) distance: 7,341 for FlowRL vs 10,640 for model-based generation, confirming better temporal alignment with real data
- Generalizable to other semi-structured domains: Robotics, smart grids, and resource-constrained environments beyond DVFS

## Abstract

Reinforcement learning (RL) in few-shot scenarios with limited sensor data is challenging due to insufficient training samples, particularly in applications like Dynamic Voltage and Frequency Scaling (DVFS) where sensor readings are semi-structured with inherent correlations. We propose Flow-Augmented Reinforcement Learning (FlowRL), a novel method that leverages continuous normalizing flows to generate high-quality synthetic data for few-shot RL.

By integrating latent space bootstrapping for diversity and feature-weighted flow matching to preserve critical data correlations, FlowRL enhances sample efficiency and policy robustness. The method addresses limitations of conventional model-based RL methods, which often struggle with overfitting and feature correlation when applied to semi-structured sensor data, increasing feature correlation in synthetic data and reducing resemblance to real scenarios.

Evaluated on a DVFS case study using the NVIDIA Jetson TX2, our approach achieves up to 35% higher frame rates and faster Q-value convergence compared to baselines (DynaQ, PlanGAN, MAML, zTT), demonstrating its effectiveness in resource-constrained environments. By generating synthetic data that closely mirrors real-world sensor distributions, FlowRL enhances the adaptability and performance of RL models in few-shot settings, particularly valuable in dynamic environments like DVFS where real-time adaptability is critical.

FlowRL generalizes to other semi-structured domains, such as robotics and smart grids, offering a scalable solution for data-scarce RL settings. The approach draws inspiration from the Law of Large Numbers (LLN) to guide distribution approximation, though non-i.i.d. synthetic data limits strict convergence, requiring empirical validation to ensure practical improvements in policy performance.

## Contributions

1. **Flow-Augmented Reinforcement Learning (FlowRL) framework**: Novel method combining continuous normalizing flows (CNFs) with Q-learning to generate synthetic semi-structured sensor data for few-shot RL, improving sample efficiency without requiring extensive datasets or environment simulations

2. **Latent space bootstrapping technique**: Resampling method generating B bootstrapped samples {x^b_0}^B_b=1 from base latent distribution to enhance trajectory diversity and mitigate overfitting, extending flow matching objective to: min_W (1/B) Σ^B_b=1 E_{t∈[0,1], x^b_t∼p^b_t(x)} [‖v_W(x^b_t) - w(x^b_t)‖^2]

3. **Feature-weighted conditional flow matching**: Random Forest-based importance weighting (λ_i = Importance(x_t,i) / Σ_j Importance(x_t,j)) prioritizes critical features (e.g., temperature, power consumption) in synthetic data generation via objective: min_W (1/B) Σ^B_b=1 E [Σ^d_i=1 λ_i‖v_W(x^b_t,i; z) - w(x^b_t,i; z)‖^2]

4. **Redefined regret function for synthetic data**: Theoretical analysis extending cumulative regret to account for estimation error from combined real (D_R, size n) and synthetic (D_S, size m) data: Reg(T) = Σ^T_t=1 [Q*(s_t, a_t) - Q^π(s_t, a_t)] + O(σ√(log(1/δ)/(n+m))), demonstrating sublinear regret with reduced sample complexity

5. **Empirical validation of superior data quality**: FlowRL preserves feature correlations better than model-based approaches (visual correlation matrix analysis), achieves lower DTW distance (7,341 vs 10,640), broader distributional coverage (histogram analysis), and better real-data alignment (t-SNE visualization)

6. **Comprehensive experimental validation on DVFS**: Experiments on NVIDIA Jetson TX2 demonstrate FlowRL achieves up to 35% higher FPS, faster Q-value convergence, stable rewards, and outperforms all baselines (zTT, DynaQ, PlanGAN, MAML) with comprehensive ablation studies confirming necessity of both bootstrapping and feature weighting

## Methodology

**Problem Formulation**:

FlowRL addresses few-shot reinforcement learning with semi-structured sensor data, where limited real-world trajectories hinder RL agent training. The goal is to augment real data D_R (size n) with synthetic data D_S (size m) that closely approximates the true distribution p(s_t, a_t, s_{t+1}), improving sample efficiency and policy robustness.

**Semi-Structured Sensor Data**: Data exhibiting correlations and noise but lacking predefined relational structures (e.g., DVFS sensor readings: FPS, frequency f, power ρ, temperature θ). Unlike grid-structured data (images) or fully unstructured text, semi-structured data requires methods that preserve feature correlations during augmentation.

**Core Components**:

**1. Flow Matching for Generative Modeling**:

Flow matching models continuous evolution of probability density from base distribution (N(0, I) at t=0) to target distribution (at t=1) via ordinary differential equation:
- dxt/dt = w(xt, t)
- w(·, ·): Time-dependent velocity field for sample vector xt at time t
- Parametric network v_W trained to minimize: min_W E_{xt∼pt} [‖v_W(xt, t) - w(xt, t)‖^2]

**Advantages over Diffusion Models**:
- Convex training objective (no adversarial training)
- Simulation-free training of CNFs
- More efficient sampling (fewer integration steps)
- Better sample quality with fewer iterations

**Direct Joint Distribution Modeling**: Unlike model-based RL which predicts next states s_{t+1} given (s_t, a_t) via learned model P̂(s_{t+1}|s_t, a_t), flow matching generates synthetic tuples (s_t, a_t, s_{t+1}) directly from joint distribution p(s_t, a_t, s_{t+1}), avoiding error accumulation from sequential predictions.

**2. Latent Space Bootstrapping** (Algorithm 1, lines 8-9):

**Motivation**: Standard flow matching may produce limited diversity, causing RL agents to overfit to narrow data regions.

**Approach**: For sample x_0 ∼ p_0(x) from base latent distribution (initial tuple (s_0, a_0, s_1)), generate B bootstrapped samples via resampling:
- x^b_0 = Resample(x_0), b = 1, ..., B

**Bootstrapped Flow Matching Objective** (Equation 2):
```
min_W (1/B) Σ^B_b=1 E_{t∈[0,1], x^b_t∼p^b_t(x)} [‖v_W(x^b_t) - w(x^b_t)‖^2]
```

**Effect**: Promotes diverse synthetic trajectories by leveraging resampled latent distributions, ensuring broader coverage of joint state-action-next-state space. This addresses limited exploration in real data collection (e.g., rarely sampled CPU frequencies in DVFS).

**3. Feature Weighting via Random Forest**:

**Motivation**: Bootstrapping adds diversity but may dilute critical feature relationships. Feature weighting ensures synthetic data resembles real unstructured data by prioritizing important features.

**Random Forest Feature Importance** (Equation 3):
- Train Random Forest regressor/classifier on real data to predict next-state transitions
- Feature importance at time t for dimension i: Importance(x_t,i) based on impurity reduction (classification) or variance reduction (regression)
- Normalize importance: λ_i = Importance(x_t,i) / Σ^d_j=1 Importance(x_t,j), i = 1, ..., d

**Interpretation**: Features with large λ_i (e.g., temperature in DVFS) are emphasized during training, preserving critical data relationships. Importance can be updated periodically or assumed constant if ranks are stable.

**4. Conditional Flow Matching with Feature Weighting** (Equation 4):

**Setup**: Incorporate feature weights λ_i into conditional flow matching with context z ∼ q(z) (e.g., rewards, auxiliary variables).

**Weighted Conditional Objective**:
```
min_W (1/B) Σ^B_b=1 E_{t∈[0,1], x^b_t∼p^b_t(x), z∼q(z)} [Σ^d_i=1 λ_i‖v_W(x^b_t,i; z) - w(x^b_t,i; z)‖^2]
```

**Effect**: High-importance features (e.g., temperature, resource usage) are prioritized during training, preserving essential correlations in joint distribution p(s_t, a_t, s_{t+1}) for robust synthetic data generation in semi-structured settings like DVFS.

**5. Theoretical Motivation - Regret Bounds**:

**Model-Free RL Framework** (Section 3.5):

Synthetic data generated by flow matching augments real data D_R to improve sample efficiency and reduce variance in Q-value estimates. The redefined cumulative regret up to time T:

```
Reg(T) = Σ^T_t=1 [Q*(s_t, a_t) - Q^π(s_t, a_t)] + O(σ√(log(1/δ)/(n+m)))
```

Where:
- Q*: Optimal Q-function
- Q^π: Q-function under learned policy π
- σ^2: Bounded variance of rewards and Q-values (Var[r + γ max Q] ≤ σ^2)
- δ: Confidence parameter
- Second term: Estimation error due to finite real (n) and synthetic (m) samples

**Key Insight**: Synthetic data contribution to m ensures sublinear regret Reg(T) = O(√T) with reduced sample complexity. The variance term reduces from O(σ√(log(1/δ)/n)) (real-data-only) to O(σ√(log(1/δ)/(n+m))) (augmented), outperforming real-data-only training.

**Enhanced Model Realizability** (Section 3.4):

Empirical Bellman operator for learned MDP model M̂ estimated using D_R and D_S:
```
T̂Q(s, a) = (1/(n+m)) (Σ_(s',r)∈D_R [r + γ max_a' Q(s', a')] + Σ_(s'',r)∈D_S [r + γ max_a' Q(s'', a')])
```

Under bounded variance, concentration bound gives with probability 1-δ:
```
‖T̂Q - TQ‖ ≤ O(σ√(log(1/δ)/(n+m)))
```

**Law of Large Numbers (LLN) Connection**: With sufficiently diverse and representative data (via bootstrapping), empirical estimates Q_T converge to true values as T → ∞. Synthetic data improves realizability by expanding coverage of (s, a) pairs sampled with probability μ > 0.

**Practical Caveats**:
- Non-i.i.d. synthetic data limits strict LLN convergence
- High-dimensional settings (DVFS) constrain exploration
- Real-synthetic mismatch may arise if flow model fails to capture corner cases
- Feature weighting mitigates this by ensuring synthetic tuples resemble unstructured data

**Bias arises if synthetic tuples deviate from true distribution**. Feature weighting ensures fidelity to semi-structured data distributions, keeping bias minimal, making flow matching-augmented model-free RL effective for few-shot settings.

**6. Algorithm 1: Distribution-Aware Flow Matching for Few-Shot Online RL**:

**Inputs**:
- D_real: Real data replay buffer
- D_syn: Synthetic data replay buffer
- DQN: Deep Q-Network agent
- FM: Flow Matching model

**Procedure**:
```
1: Initialize: D_real, D_syn, DQN, FM
2: for i = 1 to T do
3:   Collect real tuple τ_real = (s_t, a_t, s_{t+1}) and store in D_real
4:   if enough new data in D_real then
5:     Train FM using Equation (4)  # Feature-weighted conditional flow matching
6:   end if
7:   for j = 1 to rollout_count do
8:     x^b_0 = Resample(x_0)  # Bootstrap tuple (s_0, a_0, s_1)
9:     Evolve x^b_0 to x^b_t via FM  # Generate tuple (s_t, a_t, s_{t+1})
10:    Store synthetic tuple τ_syn = (s_t, a_t, s_{t+1}) in D_syn
11:  end for
12:  if enough data in D_real and D_syn then
13:    Sample mini-batches and update DQN
14:  end if
15: end for
```

**Key Steps**:
- **Line 3**: Collect real environment interactions (online RL)
- **Lines 4-6**: Retrain/fine-tune FM when sufficient new data available
- **Lines 7-11**: Generate synthetic tuples via bootstrapping and flow evolution
- **Lines 12-14**: Update DQN using combined real and synthetic mini-batches

**Dual Replay Buffer Design**: Separate D_real and D_syn ensures traceability and allows different sampling strategies (e.g., prioritize real data for stability, synthetic for exploration).

**7. DVFS Case Study - Experimental Setup**:

**Platform**: NVIDIA Jetson TX2 (power-efficient embedded platform)
- 6 heterogeneous CPU cores (4× ARM Cortex-A57 + 2× Denver2)
- Embedded GPU (256-core Pascal)
- Thermal throttling at >50°C (mitigated with 5-10s sleep mode)

**Task**: Optimize system performance under thermal and power constraints by dynamically adjusting frequency and power settings via Deep Q-Network (DQN) agent.

**Framework**: Based on zTT framework [Kim et al., MobiSys 2021] for learning-based DVFS with zero thermal throttling.

**Client-Server Architecture**:
- **Client** (Jetson TX2): Collects semi-structured state tuples {FPS, f, ρ, θ} at 1-second intervals
  - FPS: Frames per second (responsiveness metric)
  - f: Core frequency (MHz)
  - ρ: Power consumption (Watts)
  - θ: Core temperature (°C)
- **Server**: Assigns frequency actions from discrete set defined by GPU_CLOCK_LIST hardware constraints

**Episode Structure**:
- 10 tuples per episode (collected at 1s intervals)
- 90-second horizon (90 decision points per episode)
- Thermal monitoring: Sleep mode if temperature exceeds 50°C (5-10s)

**Data Preprocessing**:
- Min-max normalization for all state features
- Ensures uniform scaling across FPS, frequency, power, temperature

**DQN Architecture**:
- Neural network: 2 hidden layers, 6 neurons each
- Loss: Mean Squared Error (MSE)
- Optimizer: Adam
- Exploration: ε-greedy (ε = 1.0 initial, decay over time)
- Target network: Slower-moving parameters W^-

**Q-Learning Update Rule**:
```
Q(s_t, a_t; W) ← Q(s_t, a_t; W) + α[r_t + γ max_a' Q(s_{t+1}, a'; W^-) - Q(s_t, a_t; W)]
```

**Reward Function** (adapted from zTT [14]):
```
R = u + v + β/ρ
```

Where:
- u = 1 if FPS ≥ target_FPS, else FPS/target_FPS
- v = 0.2·tanh(target_temp - θ) if θ < target_temp, else -2
- Target FPS: 30
- Target temperature: 50°C
- β: Reward scale parameter (tuned values: {0, 2, 4})

**Design Rationale**: Balances performance (FPS), thermal safety (temperature penalty), and energy efficiency (power term).

**8. Hyperparameter Tuning** (Table 1):

**Method**: Grid search based on reward stability and convergence speed.

**Key Hyperparameters**:
- Experiment Time: {50, 90, 200, 400, 800} seconds
- Target FPS: {30, 60, 90}
- Target Temperature: {50, 60}°C
- Learning Rate: {0.01, 0.05, 0.1} → Selected: 0.05 (dynamically reset to 1 under specific reward conditions)
- Discount Factor (γ): {0.95, 0.99}
- Epsilon Decay: {0.95, 0.99}
- Batch Size: {32, 64, 128}
- Agent Train Start: {32, 40, 80, 100}
- Planning Iterations: {100, 200, 1000}
- Model Train Start: {32, 200, 300}
- Reward Scale (β): {0, 2, 4}

**Note**: Learning rate 0.05 may seem high compared to typical deep learning ranges (1e-4 to 5e-5). Future work will explore random search (e.g., Hyperopt) for refinement.

**Training Intervals**:
- **Agent Train Start**: Initiates Q-network updates from replay buffers
- **Planning Iterations**: Controls MDP model updates for model-based baselines
- Mini-batches sampled from combined D_real and D_syn

**9. Performance Metrics**:

- **Frames Per Second (FPS)**: Primary responsiveness metric (higher is better)
- **Power Consumption (ρ)**: Energy efficiency (lower is better, included in reward via β/ρ term)
- **Average Temperature (θ)**: Thermal safety (lower is better, penalized if exceeds target)
- **Average Maximum Q-values**: Policy convergence indicator (higher and stable indicates learned optimal policy)
- **Emphasis**: FPS and β/ρ term for balancing performance and energy efficiency

## Results

**1. Correlation Matrix Analysis** (Figure 2):

**Purpose**: Evaluate how well synthetic data captures feature correlations using Pearson's correlation coefficient:
```
corr_ij = Σ^n_k=1 (x_i,k - x̄_i)(x_j,k - x̄_j) / √(Σ^n_k=1 (x_i,k - x̄_i)^2) √(Σ^n_k=1 (x_j,k - x̄_j)^2)
```

**Comparison**: Four correlation matrices computed (11 features each: Freq, Power, Temp, FPS, Freq Actions, Rewards, Next Freq, Next Power, Next Temp, Next FPS):
- **Model-Based (Real)**: Correlation matrix from real data used for model-based RL
- **FlowRL (Real)**: Correlation matrix from real data used for FlowRL
- **Model-Based (Gen)**: Correlation matrix from model-based generated synthetic data
- **FlowRL (Gen)**: Correlation matrix from FlowRL generated synthetic data

**Key Findings**:

- **FlowRL (Gen) better matches real data**: Visual inspection shows FlowRL-generated data captures feature correlations more accurately than model-based approach
- **Model-based approach shows misplaced correlations**: Dark regions in incorrect locations indicate potential overfitting to specific patterns and misrepresenting genuine dependencies (e.g., artificially strong correlations between unrelated features)
- **Standard flow matching (not shown) appears uniform**: Less differentiated correlation matrix suggests critical feature relationships are weakened or diluted
- **FlowRL balances extremes**: Retains essential correlations without artificially magnifying or suppressing them, avoiding both underfitting (standard flow matching) and overfitting (model-based)

**Interpretation**: Feature-weighted conditional flow matching (Equation 4) successfully preserves critical data correlations (e.g., temperature-frequency, power-frequency dependencies in DVFS), essential for robust RL policy training.

**2. t-SNE Visualization** (Figure 4):

**Purpose**: Compare distributional coverage of synthetic data generation methods in 2D latent space.

**Methods Compared**:
- **Model-Based**: Synthetic data from learned MDP model P̂(s_{t+1}|s_t, a_t)
- **FlowRL**: Synthetic data from flow matching with weighted and bootstrapped features
- **Random**: Random sampling from real data distribution

**Key Findings**:

- **FlowRL covers real data and more**: t-SNE plot shows FlowRL synthetic data (blue) encompasses real data points (red) and extends beyond, indicating superior generalization
- **Random sampling restricted to real data distribution**: Does not explore beyond observed samples, limiting RL agent's ability to handle edge cases
- **Model-based approach does not cover real data**: Synthetic data clusters separate from real data, indicating distributional mismatch and potential overfitting to narrow regions

**Interpretation**: FlowRL's latent space bootstrapping (Equation 2) successfully generates diverse synthetic trajectories that generalize beyond original dataset, while feature weighting ensures fidelity to real data distributions.

**3. Dynamic Time Warping (DTW) Evaluation**:

**Purpose**: Assess temporal alignment between synthetic and real data sequences.

**DTW Distance** (lower is better):
- **Model-Based**: 10,640
- **FlowRL (weighted + bootstrapped)**: 7,341

**Improvement**: FlowRL achieves 30.9% lower DTW distance (3,299 reduction), confirming better temporal alignment with real sensor data sequences.

**Interpretation**: Continuous normalizing flows (CNFs) via flow matching model temporal evolution dxt/dt = w(xt, t) more accurately than discrete model-based predictions, preserving sequential dependencies in semi-structured sensor data.

**4. Distributional Coverage - Histogram Analysis** (Figure 5):

**Purpose**: Examine coverage of key state-action features under different synthetic data generation methods.

**Features Analyzed** (10 histograms):
- **State features**: Freq, Power, Temp, FPS
- **Action**: Freq Action
- **Reward**: Rewards
- **Next-state features**: Next Freq, Next Power, Next Temp, Next FPS

**Key Findings**:

- **FlowRL covers wider spectrum**: Histograms show FlowRL (orange) spans broader value ranges across all features compared to model-based (blue)
- **Model-based narrowly concentrated**: Particularly in power states and next states, suggesting overfitting and failure to generalize to broader real-world scenarios
- **Standard flow matching (not shown) less coverage than FlowRL**: Does not exhibit extreme concentrations like model-based but still inferior to FlowRL
- **Critical for edge case robustness**: Expanded coverage helps RL agent explore and learn policies robust to varied conditions (e.g., high temperature spikes, power fluctuations)

**Interpretation**: Bootstrapping enhances diversity (broader histograms) while feature weighting prevents dilution of critical relationships (maintains meaningful distributions).

**5. Performance Comparisons - FPS, Reward, Q-Value** (Figure 3):

**Three Subplots** (x-axis: time in seconds, 0-90s; each second = 1 iteration based on FPS):

**Subplot (a): Model-Free Ablations (zTT variants)**:
- **Methods**: zTT, zTT Large (extended features from profiling data), Norm zTT Large (with normalization)
- **Findings**: No specific differences in average reward, Q-values, and frame rate across variants
- **FPS**: Stable around 10 FPS for all three
- **Interpretation**: Extended features and normalization alone insufficient to improve performance without data augmentation

**Subplot (b): FlowRL Ablations vs Baselines**:
- **Methods**:
  - zTT (baseline model-free DQN)
  - FlowRL (full: weighted + bootstrapped)
  - FlowRL Not Wtd (bootstrapped only, no feature weighting)
  - FlowRL Not Wtd Not Boot (neither weighted nor bootstrapped, standard flow matching)
  - Random (random synthetic data augmentation)

- **Key Findings**:
  - **Faster convergence**: FlowRL (weighted + bootstrapped) shows fastest Q-value rise and reward stabilization (by ~40s)
  - **Higher stable FPS**: FlowRL reaches ~25 FPS by 90s, compared to ~10-15 FPS for other methods
  - **Ablation confirms necessity of both components**:
    - FlowRL Not Wtd (no weighting): Slower convergence than full FlowRL, indicating feature weighting preserves critical correlations
    - FlowRL Not Wtd Not Boot (standard flow matching): Worst among FlowRL variants, confirming bootstrapping essential for diversity
    - Random: Poor performance, confirming need for principled generative approach
  - **Q-value convergence**: FlowRL reaches higher Q-values (~8,000-10,000) faster than all baselines

**Subplot (c): Full Method Comparison**:
- **Methods**: zTT, FlowRL, MAML (meta-learning), Model-Based (DynaQ)

- **Key Results**:
  - **FPS**: FlowRL achieves ~25 FPS (highest), followed by zTT (~10 FPS), MAML (~5 FPS), Model-Based (~5 FPS)
  - **FPS Improvement**: Up to 35% higher than best baseline (comparing FlowRL ~25 FPS to zTT ~18.5 FPS at 90s)
  - **Reward**: FlowRL shows fastest stabilization and highest average reward (~2) by 90s
  - **Q-Value**: FlowRL converges fastest (by ~30-40s) and reaches highest values (~10,000), while Model-Based and MAML lag significantly (~2,000-4,000)

- **FlowRL outweighs all other approaches** across all three metrics (FPS, Reward, Q-Value)

**Statistical Significance**: Consistent superiority across 90-second episodes (90 iterations) with repeated experiments (standard practice in RL evaluations).

**6. Summary of Results**:

| Metric | FlowRL | Best Baseline | Improvement |
|--------|--------|---------------|-------------|
| FPS (90s) | ~25 | ~18.5 (zTT) | +35% |
| Max Q-Value | ~10,000 | ~4,000 (zTT) | +150% |
| Convergence Time | ~30-40s | ~60-90s (zTT) | 50% faster |
| DTW Distance | 7,341 | 10,640 (Model-Based) | -30.9% |
| Correlation Preservation | High (visual) | Low (Model-Based) | Qualitative |
| Distributional Coverage | Wide (histograms) | Narrow (Model-Based) | Qualitative |

**Comprehensive Ablation Analysis**:
- **Weighted + Bootstrapped (Full FlowRL)**: Best performance
- **Bootstrapped only (No Weighting)**: Moderate performance, confirms weighting preserves correlations
- **Standard Flow Matching (No Boot, No Wtd)**: Worst FlowRL variant, confirms bootstrapping essential
- **Random Augmentation**: Poor performance, confirms need for principled approach

**Comparison with State-of-the-Art**:
- **MAML**: Meta-learning approach fails to adapt quickly in DVFS setting (low FPS, slow convergence)
- **Model-Based (DynaQ)**: Suffers from overfitting and poor generalization (misplaced correlations, narrow coverage)
- **zTT**: Best model-free baseline but lacks data augmentation benefits of FlowRL

## Notes

**Research Context**:
- **Publication Type**: arXiv preprint arXiv:2409.14178v3 (v3 submitted January 10, 2026)
- **Original Submission**: September 2024 (arXiv:2409.14178v1)
- **Sole Author**: Mohammad Pivezhandi (first and sole graduate student author)
- **Co-Author**: Abusayeed Saifullah (PhD advisor at UT Dallas)
- **Affiliation**: Wayne State University, Detroit, MI, USA (Mohammad), University of Texas at Dallas, Richardson, TX, USA (Saifullah)

**Note on Advisor Affiliation**: Saifullah moved from Wayne State University to UT Dallas, explaining institutional difference from earlier papers (bashir2022energy, bhuiyan2023precise, pivezhandi2023toward).

**PhD Research Arc**:
- Follows pivezhandi2023toward (2023 M.S. thesis on hierarchical multi-agent RL for DVFS)
- Extends energy-aware scheduling with novel data augmentation approach for few-shot RL
- Part of broader PhD research on "Data-Efficient AI-Guided Energy- and Thermal-Aware Scheduling"
- Addresses sample efficiency challenge in RL for embedded systems

**Technical Innovation**:

**1. Flow Matching for RL Data Augmentation** (First Application):
- Novel application of continuous normalizing flows (CNFs) to RL data augmentation
- Prior work: Flow matching for image/video generation (diffusion models), not RL
- Key advantage: Direct joint distribution modeling p(s_t, a_t, s_{t+1}) vs sequential model-based predictions

**2. Latent Space Bootstrapping**:
- Inspired by Efron's bootstrap [9] but applied to latent space of generative models
- Generates B bootstrapped samples {x^b_0}^B_b=1 for diversity
- Addresses limited exploration in few-shot RL (e.g., rarely sampled CPU frequencies)

**3. Feature-Weighted Flow Matching**:
- Random Forest feature importance for prioritizing critical features
- Weighted conditional objective (Equation 4) ensures synthetic data preserves correlations
- Distinguishes from standard conditional flow matching which treats all features equally

**4. Hybrid Real-Synthetic Training**:
- Dual replay buffer design (D_real + D_syn)
- Theoretical regret bounds for combined data (Section 3.5)
- Empirical validation of synthetic data quality (correlation, DTW, histograms, t-SNE)

**Application Domain**:
- **Primary**: Dynamic Voltage and Frequency Scaling (DVFS) for embedded systems
- **Platform**: NVIDIA Jetson TX2 (6 CPU cores, embedded GPU, thermal constraints)
- **Generalization**: Robotics (sensor-based control), Smart Grids (demand-response), IoT devices (resource-constrained)

**Comparison with Related Work**:

**DVFS-Specific**:
- zTT [Kim et al., MobiSys 2021]: Model-free DQN baseline, no data augmentation
- Lin et al. [MobiCom 2023]: Workload-aware DVFS, no few-shot learning
- Zhou & Lin [IEEE TNSE 2021]: Deadline-aware deep recurrent Q-network
- Zhang et al. [IEEE TMC 2024]: DVFO for edge-cloud collaborative inference

**Few-Shot RL Methods**:
- MAML [Finn et al., ICML 2017]: Meta-learning, adapts to new tasks with minimal data but struggles in DVFS (low FPS, slow convergence)
- Model-Based RL [Moerland et al., FnTML 2023]: Approximates transition dynamics, suffers from overfitting (narrow distributional coverage)
- PlanGAN [Charlesworth & Montana, NeurIPS 2020]: Generative model-based planning with dual-memory, inferior to FlowRL

**Data Augmentation for RL**:
- DynaQ [Peng et al., arXiv 2018]: Integrates planning with model-based data generation, but model-based approach has limitations
- FlowRL distinguishes by using generative modeling (flow matching) instead of learned environment models

**Statistical Learning for DVFS**:
- Sasaki et al. [CF 2007]: Decision trees for energy-efficient DVFS lookup
- Liu et al. [IEEE TCAD 2021]: CARTAD with compiler-assisted f-score ranking
- FlowRL advances by integrating feature weighting with Random Forests for data augmentation

**Regret Bounds**:
- Jin et al. [NeurIPS 2018]: Sublinear regret O(√T) for tabular Q-learning
- Fan & Ming [ICML 2021]: Model-based RL with posterior sampling
- FlowRL extends to continuous high-dimensional settings with empirical validation

**Limitations**:

1. **Non-i.i.d. Synthetic Data**: LLN convergence not guaranteed, relies on empirical validation
2. **High Learning Rate**: 0.05 may seem high, future work to explore random search (Hyperopt)
3. **Platform-Specific**: DVFS experiments on Jetson TX2, generalization to other platforms needs validation
4. **Hyperparameter Sensitivity**: Grid search over 11 hyperparameters, optimal values may vary by application
5. **Thermal Throttling Mitigation**: Sleep mode (5-10s) when temperature exceeds 50°C may affect fair comparisons
6. **Limited Baseline Comparison**: MAML and PlanGAN not originally designed for DVFS, may underperform in this domain

**Future Directions** (Section 6 - Conclusion):

1. **Advanced RL Algorithms**: Explore PPO (Proximal Policy Optimization) instead of DQN for better sample efficiency
2. **Neural-Based Feature Weighting**: Replace Random Forest with learned attention mechanisms [45] for dynamic feature importance
3. **Multi-Domain Validation**: Extend FlowRL to robotics (sensor-based control), smart grids (demand-response), IoT devices
4. **Theoretical Guarantees**: Formal convergence analysis for non-i.i.d. synthetic data under mixing conditions
5. **Hyperparameter Optimization**: Random search (Hyperopt) or Bayesian optimization for better hyperparameter tuning
6. **Real-World Deployment**: Long-term studies on production embedded systems beyond 90-second episodes

**Complementary Research** (Referenced in Introduction):

Related work by Mohammad Pivezhandi:
- Hierarchical multi-agent DVFS scheduling [4] - pivezhandi2026hidvfs
- Zero-shot LLM-guided allocation [5] - pivezhandi2026zerodvfs
- Statistical feature-aware task allocation [6] - pivezhandi2025feature
- Graph-driven performance modeling [7] - pivezhandi2025graphperf

**Code Availability**: Not mentioned in paper (arXiv preprint, code may be released upon publication)

**Reproducibility**:
- Detailed hyperparameter table (Table 1)
- Platform specifications (NVIDIA Jetson TX2)
- Framework details (zTT-based, DQN architecture)
- Reward function explicitly stated
- Comprehensive ablation studies

**Figures Summary**:
- Figure 1: Workflow diagram (client-server architecture, flow matching generator)
- Figure 2: Correlation matrix comparisons (4 matrices: Model-Based/FlowRL × Real/Gen)
- Figure 3: Performance comparisons (FPS, Reward, Q-Value over 90s, 3 subplots)
- Figure 4: t-SNE visualization (Model-Based, FlowRL, Random coverage)
- Figure 5: Distribution histograms (10 features: FlowRL vs Model-Based)

**Tables Summary**:
- Table 1: Hyperparameters and tuning values (11 hyperparameters with value ranges)

**Mathematical Notation**:
- s_t, a_t, s_{t+1}: State, action, next-state tuples
- D_R, D_S: Real and synthetic replay buffers (size n, m)
- Q*(s, a): Optimal Q-function
- Q^π(s, a): Q-function under learned policy π
- v_W(x_t, t): Parametric velocity field (flow matching)
- w(x_t, t): Target velocity field
- λ_i: Feature importance weight for dimension i
- Reg(T): Cumulative regret up to time T
- σ^2: Bounded variance of rewards and Q-values
- δ: Confidence parameter

**Connection to Research Arc**:

Timeline:
1. 2015: ML/pattern recognition (pivezhandi2015statistical)
2. 2016: ASIP/CORDIC design (pivezhandi2016asip)
3. 2020: FPGA event-based vision (pivezhandi2020parahist)
4. 2023: Hierarchical RL for DVFS (pivezhandi2023toward - M.S. thesis)
5. **2024: Flow-augmented few-shot RL (this paper)**
6. 2024-2026: Zero-shot LLM, graph-driven models, feature-aware allocation

FlowRL represents a **methodological advancement** in Mohammad's PhD research, addressing sample efficiency for energy-aware RL scheduling through novel data augmentation with continuous normalizing flows.

**Advisor Transition**:
- Saifullah was co-major professor for M.S. thesis (Iowa State, 2023)
- Continued as PhD advisor at Wayne State
- Moved to UT Dallas (affiliation updated in 2024 paper)
- Consistent research direction: Energy-aware RL for embedded systems

**Publication Strategy**:
- arXiv preprint (v3 Jan 2026) suggests submission to major conference/journal
- Likely targets: NeurIPS, ICML (ML), RTAS, RTSS (real-time), MobiSys (mobile systems)
- Comprehensive experimental validation and ablations strengthen submission

## Bibdata

```bibtex
% Row 9
@article{pivezhandi2024flowrl,
  title={FlowRL: Flow-Augmented Few-Shot Reinforcement Learning for Semi-Structured Sensor Data},
  author={Pivezhandi, Mohammad and Saifullah, Abusayeed},
  journal={arXiv preprint arXiv:2409.14178},
  year={2024}
}
```
