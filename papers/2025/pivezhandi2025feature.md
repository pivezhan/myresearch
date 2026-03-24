# Feature-Aware Task-to-Core Allocation in Embedded Multi-core Platforms via Statistical Learning

**Citation Key**: `pivezhandi2025feature`

**Year**: 2025

**Type**: inproceedings

**Authors**: Pivezhandi, Mohammad and Modekurthy, Prashant and Saifullah, Abusayeed

**Venue**: IEEE RTCSA'25 (The 31st IEEE International Conference on Embedded and Real-Time Computing Systems and Applications)

---

## Key Points

- Hybrid statistical learning framework: Random Forest + backward stepwise OLS + Pearson correlation for task-to-core allocation
- Up to 10% energy reduction and 5°C temperature decrease vs random core selection
- 61.6% mean squared error reduction compared to SOTA [23] with 6% thermal prediction improvement and 16% parameter reduction
- Correlation-aware allocation: assigns tasks to thermally uncorrelated cores to reduce hotspots
- Multi-platform validation: Intel Core i7 12th Gen (14 cores), Intel Core i7 8th Gen (6 cores), Intel Xeon 2680 v3 (12 cores), NVIDIA Jetson TX2 (6 cores)
- Fewer than 8 features suffice for accurate energy and temperature prediction (RF: 39/72 profiler, 21/31 temperature)
- Bootstrapping (100 resamples) enhances model robustness and supports few-shot learning in resource-constrained systems
- Co-author: **Prashant Modekurthy** (University of Nevada, Las Vegas) - first collaboration outside Iowa State/Wayne State

## Abstract

Optimizing task-to-core allocation can substantially reduce power consumption in multi-core platforms without degrading user experience. However, existing approaches overlook critical factors such as parallelism, compute intensity, and heterogeneous core types. This paper introduces a statistical learning approach for feature selection that identifies the most influential features—such as core type, speed, temperature, and application-level parallelism or memory intensity—for accurate environment modeling and efficient energy minimization, a critical consideration for embedded systems.

Our experiments, conducted with state-of-the-art Linux governors and thermal modeling techniques, show that correlation-aware task-to-core allocation lowers energy consumption by up to 10% and reduces core temperature by up to 5°C compared to random core selection. Furthermore, our compressed, bootstrapped regression model improves thermal prediction accuracy by 6% while cutting model parameters by 16%, yielding an overall mean square error reduction of 61.6% relative to existing approaches. We provided results based on superscalar Intel Core i7 12th Gen processors with 14 cores, and validated our method across a diverse set of hardware platforms and effectively balanced performance, power, and thermal demands through statistical feature evaluation.

## Contributions

1. **Hybrid statistical learning framework**: First integration of Random Forest (embedded), backward stepwise OLS (wrapper), and Pearson correlation analysis (filter) for feature-aware task-to-core allocation in embedded systems, surpassing prior single-method approaches
2. **Correlation-aware allocation algorithm**: Statistically reduces temperature and energy by assigning tasks to thermally uncorrelated cores (Equation 11), validated across heterogeneous embedded platforms with per-core and cluster-level thermal sensors
3. **Bootstrapping for data augmentation**: Employs 100 bootstrap resamples to augment datasets and boost accuracy, supporting few-shot and meta-learning in resource-constrained embedded settings
4. **Rigorous multi-platform validation**: Achieves up to 10% energy reduction, 5°C temperature decrease, 6% thermal prediction improvement, 16% parameter reduction, and 61.6% MSE improvement over SOTA [23] across Intel Core i7 8th/12th Gen, Intel Xeon 2680, and NVIDIA Jetson TX2
5. **Feature compression**: RF-based feature selection reduces profiler features from 72 to 39 and temperature features from 31 to 21 with minimal accuracy loss (Figure 2), enabling real-time embedded deployment
6. **Environment modeling**: Fully Connected Neural Networks (FCN) trained on compressed feature sets predict energy consumption and core temperatures with MSE 0.9640 (temperature) and 2.4669 (profiler) on Intel Core i7 12th Gen (Table 4)

## Methodology

**Overall Approach**:
This paper develops a robust multi-stage methodology based on three feature selection approaches—embedded (Random Forest), wrapper-based (backward stepwise OLS), and filter-based (Pearson correlation)—to inform intelligent task-to-core allocation algorithms for embedded multi-core systems. The hybrid framework integrates Random Forest, backward stepwise OLS, and Pearson correlation, surpassing traditional filter-based methods in efficiency and predictive power.

**Multi-Platform Environment Setup**:
The methodology accommodates diverse thermal sensor granularities:
- **Per-core sensors**: Intel Core i7 8th Gen (6 cores), Intel Xeon 2680 v3 (12 cores) provide individual core temperature readings, enabling detailed per-core correlation analysis
- **Heterogeneous cores**: Intel Core i7 12th Gen (14 cores: 8 P-cores, 6 E-cores) with per-core sensors for hybrid architecture
- **Cluster-level sensors**: NVIDIA Jetson TX2 (6 cores, big-LITTLE: Denver + A57 clusters) provides cluster-aggregated temperature via `/sys/class/thermal`, suitable for coarse-grained allocation

**3-Stage Feature Selection Framework**:

**Stage 1: Data Collection and Environment Setup**

**Data Collection**:
- **Platforms**: Intel Core i7 12th Gen (primary), Intel Core i7 8th Gen, Intel Xeon 2680 v3, NVIDIA Jetson TX2
- **Configurations**: Varying core counts (4, 6, 12, 14 cores), diverse CPU frequencies, task prioritizations
- **Benchmarks**: Barcelona OpenMP Tasks (BOTS) suite [22]
  - Fibonacci (fib): Compute-intensive recursive task
  - Matrix multiplication (Strassen): Memory-intensive
  - Sequence alignment (alignment): Mixed compute and memory access patterns
- **Performance metrics**: CPU frequency, utilization, memory bandwidth, cache misses (via `perf` and `cpufreq-info`)
- **Energy monitoring**: `/sys/class/powercap/intel-rapl/` (Intel), on-board power sensors (Jetson TX2)
- **Temperature buffer**: Stores up to 10,000 historical temperature entries per core/cluster
- **Task history**: Captures execution parameters (energy, profiler metrics, temperature) under varying priorities and frequencies

**Baseline Strategy**:
- **Random core assignment**: Tasks run on randomly selected cores (typically half of available cores, excluding core 0 reserved for system tasks)
- **Thermal throttling protection**: Execution pauses if core temperatures exceed thresholds (e.g., 100°C for Intel Core i7), allowing cores to cool before resuming
- **Cooldown threshold**: 70°C between benchmark runs to ensure thermal stability

**Stage 2: Embedded Feature Selection Using Random Forest**

Embedded methods incorporate feature selection into model training, minimizing multiple evaluations. Random Forest (RF) regressor constructs multiple decision trees and aggregates predictions, offering low-overhead feature importance extraction.

**Random Forest Algorithm**:
1. **Bootstrap Sampling**: Generate N bootstrap samples from original dataset
2. **Tree Construction**: For each bootstrap sample, grow regression tree by selecting random subset of features at each node (often √d features). Each tree grown to maximum depth without pruning
3. **Prediction Aggregation**: For regression tasks, final prediction is average of individual tree outputs:

   **ŷ = (1/N) Σᵢ₌₁ᴺ ŷᵢ** (Equation 1)

**Feature Importance Computation**:
Random Forests provide importance measure by evaluating total decrease in node impurity across all trees. For regression trees, impurity measured by residual sum of squares. Importance score Iⱼ for feature xⱼ:

   **Iⱼ = (1/N) Σᵢ₌₁ᴺ Σₜ∈ₜᵢ ΔIₜ,ⱼ** (Equation 2)

where ΔIₜ,ⱼ is impurity decrease at node t when splitting on xⱼ, and Tᵢ is set of nodes in i-th tree. Ranking features by importance scores guides selection of highly influential predictors.

**Bootstrapping for Data Augmentation**:
To increase model robustness, employ bootstrapping—a resampling method drawing multiple datasets of size n with replacement. Let D be original dataset of size n. Forming B bootstrap samples {D₁, ..., Dᴮ} helps estimate variance and stabilize final model through aggregation of predictions (100 resamples used in experiments).

**Environment Modeling with Feature Selection**:
After identifying significant features using RF importance scores, build predictive models for environment modeling—particularly neural networks—tailored to embedded constraints. Let x ∈ ℝᵖ (with p < d) denote reduced feature set. Train Fully Connected Neural Network (FCN) with multiple layers and non-linear activations to predict energy consumption or temperature. Network trained by minimizing mean squared error (MSE):

   **L(θ) = (1/n) Σᵢ₌₁ⁿ (yᵢ - f(xᵢ; θ))²** (Equation 3)

where f(xᵢ; θ) is network's output for input xᵢ with parameters θ, and yᵢ is true label. By restricting input to smaller set of highly relevant features, FCN requires fewer parameters and less computation, making it feasible for real-time applications.

**Results (Figure 2 - Intel Core i7 12th Gen)**:
- **Profiler model**: Using 39 out of 72 predictors provides nearly same CV error (optimal at ~50 features, minimal at 39)
- **Temperature model**: Using 21 out of 31 features yields optimal Cross-Validation error (CV error ~0.0107), validated for embedded efficiency

**Stage 3: Wrapper-Based Feature Selection (Backward Stepwise OLS)**

Wrapper methods evaluate feature subsets using predictive model. Because they account for feature interactions, they typically yield higher accuracy than filter methods but may be more computationally expensive. Employ backward stepwise selection algorithm starting with all features and iteratively removing least significant feature based on specified criterion. Use Ordinary Least Squares (OLS) regression model to predict target variables (energy consumption, average temperature) and assess feature significance using statistical tests.

**Backward Stepwise Selection Algorithm**:
Let F = {x₁, x₂, ..., xₐ} denote full feature set. Algorithm proceeds:

1. **Initial Model**: Fit OLS regression using all features in F:

   **y = β₀ + Σᵢ₌₁ᵈ βᵢxᵢ + ε** (Equation 4)

   where y is target variable, β₀ is intercept, βᵢ are coefficients, ε is error term

2. **Evaluate Feature Significance**: For each feature xᵢ, compute t-statistic and corresponding p-value. T-statistic for coefficient βᵢ:

   **tᵢ = β̂ᵢ / SE(β̂ᵢ)** (Equation 5)

   where β̂ᵢ is estimated coefficient, SE(β̂ᵢ) is standard error

3. **Feature Elimination**: Identify feature with highest p-value (least significant) exceeding predefined significance level (e.g., α = 0.05). Remove this feature from model

4. **Iterative Refinement**: Refit OLS model using reduced feature set and repeat steps 2-3 until all remaining features statistically significant

5. **Model Selection Criteria**: At each iteration, evaluate model using multiple metrics

**Evaluation Metrics**:

- **Akaike Information Criterion (AIC)**: Estimates relative quality of statistical models:

  **AIC = 2k - 2ln(L)** (Equation 6)

  where k is number of estimated parameters, L is maximized likelihood value. Lower AIC implies better trade-off between complexity and fit

- **Bayesian Information Criterion (BIC)**: Imposes stronger penalty on model complexity than AIC:

  **BIC = k ln(n) - 2ln(L)** (Equation 7)

  where n is number of observations. Helpful for avoiding over-complex models in resource-constrained environments

- **Mallows' Cₚ**: Assesses balance between model's complexity and fit:

  **Cₚ = RSS/σ̂² - (n - 2k)** (Equation 8)

  where RSS is residual sum of squares, σ̂² is estimate of error variance

- **Adjusted R²**: Modifies coefficient of determination (R²) to account for number of predictors:

  **Adjusted R² = 1 - ((1 - R²)(n - 1))/(n - k - 1)** (Equation 9)

  Unlike plain R², penalizes model for including uninformative features

- **Cross-Validation Error**: CV Error (K-fold CV) offers unbiased measure of out-of-sample performance, revealing model's generalization capability and mitigating overfitting concerns

**Results (Figures 3a and 3b - Intel Core i7 12th Gen)**:
- **Average temperature estimation**: Using fewer than 8 features (optimal at 8 predictors) suffices for accurate predictions across all metrics (AIC, BIC, Cₚ, Adjusted R², 5-Fold CV)
- **Energy consumption estimation**: Using 3-6 features (optimal at 3 for most metrics, 6 for Adjusted R²) provides accurate predictions, indicating tracking only most relevant predictors improves energy efficiency

**Stage 4: Filter-Based Feature Selection (Pearson Correlation)**

Filter methods assess feature relevance by examining intrinsic data properties without involving learning algorithms. Utilize Pearson correlation coefficient [21] to quantify linear relationship between core temperatures, indicating adjacency and potential heat transfer between cores, enhancing allocation decisions.

Given n observations for m cores, let θᵢ,ₖ denote temperature of core i at observation k, and θ̄ᵢ represent mean temperature of core i across all observations. Pearson correlation coefficient rᵢⱼ between cores i and j:

   **rᵢⱼ = Σₖ₌₁ⁿ(θᵢ,ₖ - θ̄ᵢ)(θⱼ,ₖ - θ̄ⱼ) / √[Σₖ₌₁ⁿ(θᵢ,ₖ - θ̄ᵢ)²] √[Σₖ₌₁ⁿ(θⱼ,ₖ - θ̄ⱼ)²]** (Equation 10)

Value of rᵢⱼ ranges from -1 to 1, where 1 indicates perfect positive linear correlation, -1 indicates perfect negative linear correlation, 0 signifies no linear correlation. High positive correlation suggests temperatures of two cores rise and fall together, possibly due to physical proximity and shared thermal characteristics.

By constructing correlation matrix **R = [rᵢⱼ]** for all pairs of cores, visualize and identify clusters of thermally correlated cores (Figure 4). Information crucial for designing task-to-core allocation strategies minimizing thermal hotspots.

**Correlation-Aware Task-to-Core Allocation Algorithm**:
Goal: assign tasks to cores that are less thermally correlated, reducing risk of localized overheating and improving overall energy efficiency.

Let C = {c₁, c₂, ..., cₘ} denote set of available cores, R be correlation matrix computed using Equation 10. Algorithm proceeds:

1. **Compute Core Correlation Scores**: For each core cᵢ, calculate correlation score sᵢ defined as average absolute correlation between core cᵢ and all other cores:

   **sᵢ = (1/(m-1)) Σⱼ₌₁ᵐ (j≠i) |rᵢⱼ|** (Equation 11)

   Lower score sᵢ indicates core cᵢ is less correlated with other cores

2. **Rank Cores Based on Correlation Scores**: Sort cores in ascending order of correlation scores to obtain ranked list Cᵣₐₙₖₑₐ

3. **Select Cores for Task Assignment**: Given number of tasks T to be assigned, select first T cores from Cᵣₐₙₖₑₐ (least correlated cores)

4. **Assign Tasks to Selected Cores**: Allocate tasks to selected cores, ensuring each task assigned to core with minimal thermal correlation to other active cores

5. **Update Temperature Buffer**: After task execution, update temperature observations θᵢ,ₖ to reflect new core temperatures, recompute correlation matrix R for subsequent allocations

**Stage 5: Summary of Multi-Stage Methodology**

1. **Data Collection**: Profile per-core or per-cluster temperatures, energy, performance under varied workloads
2. **Random Forest (Embedded)**: Prune low-importance features, optionally train FCN environment model
3. **Backward Stepwise (Wrapper)**: Further refine most energy-critical features using OLS-based elimination
4. **Filter-Based (Correlation)**: Analyze temperature correlations (per-core or per-cluster) to guide thermal-aware scheduling
5. **Task Allocation**: Prioritize assigning tasks to least-correlated units, mitigating overheating and reducing energy consumption

By unifying these steps, handle both fine-grained and coarse-grained thermal sensor inputs without losing accuracy or tractability. Random Forest provides dimensionality reduction required for backward stepwise selection to reduce computational overhead; their combination ensures only most relevant features to energy consumption remain. Filter-based approach on temperature readings further addresses correlation of cores to each other for core allocation to minimize thermal hotspots, offering novel, scalable solution validated across platforms (61.6% MSE improvement over SOTA).

## Results

**Experimental Platform and Benchmarks**:

**Primary Platform**: Intel Core i7 12th Gen with 14 cores (8 P-cores, 6 E-cores, per-core temperature via sensors), chosen for hybrid architecture

**Validation Platforms**:
- Intel Core i7 8th Gen (6 cores, per-core sensors)
- Intel Xeon 2680 v3 (12 cores, per-core sensors)
- NVIDIA Jetson TX2 (6 cores, 2 clusters—Denver and A57—with cluster-level temperature via `/sys/class/thermal`)

**Benchmarks**: Barcelona OpenMP Tasks (BOTS) suite [22]
- sparselu: Sparse LU decomposition
- nqueens: N-Queens problem
- fib: Fibonacci (compute-intensive recursive)
- strassen: Matrix multiplication (memory-intensive)
- alignment: Sequence alignment (mixed compute and memory)

**Metrics**:
- **Makespan**: Execution time in seconds
- **Energy consumption**: Microjoules via `/sys/class/powercap` (Intel), `/sys/class/thermal/energy` (Jetson)
- **Average core temperature**: °C
- Each benchmark ran 10 times per configuration, results averaged
- Temperature cooldown threshold: 70°C between runs for thermal stability

**Implementation Details**:
- **Language**: Python
- **Libraries**: scikit-learn (RF, OLS), pandas (data management), subprocess (system calls: cpufreq-set, perf stat), Parsl (parallel task execution)
- **RF parameters**: 100 trees
- **OLS parameters**: p-value threshold 0.05
- **Neural network hyperparameters**: Batch size 32, hidden neurons 64-256, epochs 50-200, learning rate 0.001-0.01 (tuned via grid search)
- **Data split**: 80% training, 20% test
- **Loss criterion**: Mean Squared Error (MSE)
- **Bootstrapping**: 100 resamples

**Feature Selection Results**:

**Table 1: Key Features Ranked by Random Forest and Backward Stepwise OLS (Intel Core i7 12th Gen)**

| Dataset | Random Forest (Top Features) | Stepwise OLS (Top Features) |
|---------|------------------------------|------------------------------|
| **Profiler** | Task ID, Actions, Temp Core 0-13, ΔTemp Core 0-13, Avg Temp, CPU Cycles, CPU Cycles Freq, Atom Cycles, Instructions, Insn per Cycle, Atom Insn, Atom Insn per Cycle, Cache Refs, Cache Refs Rate, Atom Cache Refs, Atom Cache Refs Rate, Cache Misses, Cache Miss % Refs, Atom Cache Misses, Atom Cache Miss % Refs, Cycles, Cycles Freq, Atom Cycles, Atom Cycles Freq, Branch Misses, Branch Miss % Branches, Atom Branch Misses, Atom Branch Miss % Branches, Branches, Branches Rate, Atom Branches, Atom Branches Rate, Page Faults, Page Fault Rate, Context Switches, Context Switch Rate, CPU Clock ms, CPUs Used, Task Clock ms, Task Util, Faults, Fault Rate, Sys Time s, Elapsed Time s, Energy | Task ID, Temp Core 1, Temp Core 13 |
| **Temperature** | Task ID, Actions, Temp Core 0-9, Temp Core 11-13, ΔTemp Core 0-13, Avg Temp | Task ID, Temp Core 0, Temp Core 2-4, Temp Core 7, Temp Core 10, Avg Temp |

**Key Findings**:
- **Task ID** consistently ranks high in both methods, underscoring key role in identifying energy consumption patterns
- **Temp Core 1 and Temp Core 13** emerge as significant predictors in OLS for profiler dataset, suggesting cores 1 and 13 disproportionately influence energy use due to temperature profiles
- **Allocation strategy implication**: Minimizing task assignments to cores 1 and 13 could enhance energy efficiency

**Table 2: Temperature Predictors (θᵢ) vs. Energy - Regression Statistics**

Results shown for three platforms (sorted by p-value, significant predictors highlighted):

**Xeon (12 cores)**:
- θ₁: Est. 2.35e5, t-val. 5.76, p-val. 8.48e-9 (highly significant)
- θ₉: Est. -2.53e5, t-val. -5.38, p-val. 7.38e-8 (highly significant)
- θ₇: Est. -1.56e5, t-val. -3.10, p-val. 1.93e-3 (significant)

**Core i7 8th Gen (4 cores)**:
- θ₀: Est. 3.64e3, t-val. 5.69, p-val. 1.31e-8 (highly significant)
- θ₁: Est. -9.77e3, t-val. -4.18, p-val. 2.87e-5 (highly significant)
- θ₃: Est. 8.00e3, t-val. 3.57, p-val. 3.64e-4 (significant)

**Core i7 12th Gen (14 cores)**:
- θ₂: Est. -3.79e3, t-val. -6.83, p-val. 8.41e-12 (highly significant)
- θ₀: Est. 4.87e2, t-val. 2.76, p-val. 5.81e-3 (significant)
- θ₃: Est. -5.96e3, t-val. -2.05, p-val. 4.06e-2 (marginally significant)

**Interpretation**: Low p-values indicate significant temperature predictors across platforms, with specific cores (e.g., θ₁ on Xeon, θ₂ on Core i7 12th Gen) showing strong energy impact.

**Table 3: Profiler Predictors vs. Energy - Regression Statistics**

Top predictors for three platforms (excluding temp, freq, speed; sorted by p-value):

**Xeon (12 cores)**:
- Context Switch Rate: Est. -3.07e4, t-val. -34.90, p-val. 1.34e-261 (strongest predictor)
- Cache Miss % Refs: Est. 3.32e6, t-val. 34.45, p-val. 4.02e-255
- Cache Refs: Est. 2.82e-1, t-val. 33.53, p-val. 4.94e-242
- Elapsed Time: Est. 6.44e7, t-val. 31.52, p-val. 1.52e-214

**Core i7 8th Gen (4 cores)**:
- Elapsed Time: Est. 4.85e7, t-val. 39.51, p-val. 0.00e0 (strongest predictor)
- Cache Miss: Est. 2.23e5, t-val. 21.35, p-val. 3.80e-100
- Cache Refs: Est. 6.39e-2, t-val. 16.03, p-val. 1.48e-57
- Total Cores: Est. -5.34e6, t-val. -15.47, p-val. 1.09e-53

**Core i7 12th Gen (14 cores)**:
- Elapsed Time: Est. 4.67e7, t-val. 35.82, p-val. 3.29e-275 (strongest predictor)
- Atom Branch Misses: Est. 1.41e0, t-val. 25.10, p-val. 1.21e-137
- Cache Misses: Est. 4.18e-1, t-val. 24.65, p-val. 7.37e-133
- Branch Misses: Est. 1.36e0, t-val. 22.78, p-val. 6.66e-114

**Interpretation**: Metrics like Context Switch Rate, Elapsed Time, Cache Misses show strong energy correlations across platforms, driving energy variance.

**Table 4: MSE Percentage and Parameters for Different Architectures (Intel Core i7 12th Gen)**

| Model | Temperature MSE (×100) | Temperature Params | Profiler MSE (×100) | Profiler Params |
|-------|------------------------|--------------------|--------------------|-----------------|
| FCN | 1.0299 | 2014 | 3.9047 | 3787 |
| FCN+RF | 0.9808 | 1694 | 2.4862 | 2699 |
| **FCN+RF+BS** | **0.9640** | **1694** | **2.4669** | **2699** |
| RNN | 1.0119 | 3070 | 2.8493 | 4843 |
| LSTM | 1.0307 | 9310 | 2.7778 | 15115 |
| Conv | 1.0134 | 5118 | 2.8217 | 6891 |
| Attention | 1.0143 | 6238 | 2.8933 | 8011 |
| SOTA [23] | 2.5000 | - | - | - |

**Key Findings**:
- **Best model**: FCN+RF+BS (Random Forest feature selection + bootstrapping)
  - Temperature MSE: 0.9640 (6.4% improvement over baseline FCN)
  - Profiler MSE: 2.4669 (36.8% improvement over baseline FCN)
  - Parameters: 1694 (temperature), 2699 (profiler) - lightweight for embedded systems
- **Comparison to SOTA**: FCN+RF+BS achieves 2.5000 → 0.9640 = **61.6% MSE reduction** for temperature model
- **RNN, LSTM, Conv, Attention**: All underperform FCN+RF+BS despite higher parameter counts, indicating feature selection and bootstrapping more effective than architectural complexity

**Energy and Temperature Results**:

**Figure 1: Energy Consumption Variation Across Processors**
- Platforms: Intel Core i7 12th Gen (Corei712), Intel Xeon 2680 v3 (Xeon), Intel Core i7 8th Gen (Corei78)
- Benchmarks: fib, strassen, alignment with identical frequency and core count settings
- Finding: Energy consumption varies by order of magnitude across platforms (10⁷ to 10⁸ J range), underscoring need for feature selection to pinpoint significant predictors of energy and thermal behavior

**Figure 5: Correlation-Based vs Random Core Selection (Intel Core i7 12th Gen)**
- **Correlation-based (Corr)**: Assigns tasks to thermally uncorrelated cores
- **Random (Rand)**: Assigns tasks to randomly selected cores (baseline)
- Results:
  - **Energy consumption**: Corr shows lower energy at intermediate core counts (5-9 cores), up to 10% reduction
  - **Average temperature**: Corr shows up to 5°C reduction at intermediate utilization (5-9 cores), correlation-aware allocation excels in subset scenarios (10 of 14 cores)
- Multi-platform validation: Trends reinforced across Jetson TX2, Intel Xeon 2680, Intel Core i7 8th Gen

**Figure 6: Effect of Feature Selection and Bootstrapping (Intel Core i7 12th Gen)**
- **Temperature Model MSE**: FCN (0.0103) → FCN+RF (0.0098) → FCN+RF+BS (0.0096) [6.8% total improvement]
- **Profiler Model MSE**: FCN (0.039) → FCN+RF (0.025) → FCN+RF+BS (0.025) [36% improvement]
- **Key finding**: Omitting either feature selection or bootstrapping increases MSE by 20-30%, underscoring their combined benefit

**Figure 7: Temperature Prediction (Intel Core i7 12th Gen - 10 cores shown)**
- Ground truth vs FCN vs FCN+RF predictions for Cores 0-9
- **Core 0**: Spikes to 100°C (thermal throttling region), both FCN and FCN+RF track closely
- **Cores 1-9**: Temperatures range 62-87°C, FCN+RF with reduced features (21/31) shows notable similarity to full-feature FCN predictions
- **Validation**: RF-based feature compression maintains accuracy while reducing computational load

**Figure 8: Profiler Data Prediction (Intel Core i7 12th Gen - 10 metrics shown)**
- Ground truth vs FCN vs FCN+RF for Core Cycles, Atom Cycles, Core Cache Misses, Atom Cache Misses, Core Branch Misses, Atom Branch Misses, Makespan, Energy Consumption, CPUs Utilized, Context Switches
- **Energy Consumption**: FCN+RF (39/72 features) tracks ground truth closely, validating feature reduction effectiveness
- **Core Cycles, Atom Cycles**: Both models capture temporal dynamics accurately
- **Branch Misses, Cache Misses**: FCN+RF maintains predictive accuracy despite 46% feature reduction

**Comparative Performance**:
- **10% energy reduction**: Correlation-aware allocation vs random core selection (validated on BOTS benchmarks)
- **5°C temperature decrease**: Achieved at intermediate utilization levels (5-9 cores active on 14-core system)
- **6% thermal prediction accuracy improvement**: Bootstrapping contribution (MSE 0.9808 → 0.9640)
- **16% model parameter reduction**: FCN+RF+BS (1694, 2699 params) vs baseline FCN (2014, 3787 params)
- **61.6% MSE improvement over SOTA [23]**: SOTA MSE 2.5000 → FCN+RF+BS MSE 0.9640 for temperature model

**Profiler and Temperature Models**:
- **Profiler model**: Predicts energy consumption and performance metrics (cache miss rates, branch miss rates, CPU cycles, instructions per cycle, average speed, page faults, context switches)
  - Features: 75 on Intel Core i7 12th Gen (all metrics plus current per-core temperatures θᵢ, differences Δθᵢ = θᵢ,ₜ - θᵢ,ₜ₋₁)
  - Compressed: 39 features via RF (46% reduction)
- **Temperature model**: Predicts future thermal behavior
  - Features: 30 on Intel Core i7 12th Gen (14 temperatures, 14 differences, average temperature)
  - Compressed: 21 features via RF (30% reduction)
  - Scaled: 6 features on Jetson TX2 (2 cluster temperatures, 2 differences, average) - demonstrates adaptability

**Hyperparameter Tuning**:
- Tuned to optimize convergence speed and generalization
- Models saved at optimal checkpoints (validation error plateau or sharp decline)
- Independent neural networks for temperature and profiler tasks ensured specialized performance

## Notes

**Research Context**:
- **Venue**: IEEE RTCSA'25 (31st IEEE International Conference on Embedded and Real-Time Computing Systems and Applications)
- **Publication type**: arXiv preprint arXiv:2502.15716v2 [cs.DC] 10 Jan 2026 (to appear at RTCSA 2025)
- **Primary affiliation**: Wayne State University (Mohammad Pivezhandi)
- **Co-authors**:
  - **Prashant Modekurthy** (University of Nevada, Las Vegas) - First collaboration with UNLV researcher, indicates expanding research network beyond Iowa State/Wayne State circle
  - **Abusayeed Saifullah** (University of Texas at Dallas) - PhD advisor
- **15 pages**: Comprehensive paper with 15 pages, 8 figures, 4 tables, 48 references

**Application Domains**:
- Embedded systems: Automotive, telecommunications, consumer electronics
- Real-time systems: Compute-intensive tasks on performance cores without overheating
- Mobile devices: Power-hungry applications on high-performance cores, background tasks on low-power cores
- Heterogeneous architectures: P-cores (performance), E-cores (efficiency), GPUs
- Battery-powered devices: Extending battery life through intelligent task allocation

**Technical Innovation**:
- **Hybrid framework**: First integration of three feature selection methods (embedded RF, wrapper OLS, filter correlation) for task-to-core allocation in embedded systems
- **Correlation-aware allocation**: Novel use of Pearson correlation for thermal management, assigning tasks to least-correlated cores (Equation 11)
- **Bootstrapping for few-shot learning**: 100 resamples create robust models with limited data, supporting meta-learning in resource-constrained environments
- **Multi-platform validation**: Per-core sensors (Intel homogeneous), per-core sensors (Intel heterogeneous P/E), cluster-level sensors (Jetson TX2 big-LITTLE)
- **Environment modeling**: FCN models predict energy and temperature from compressed feature sets (39/72 profiler, 21/31 temperature)

**Comparison with Prior Art**:
- **SOTA [23]**: Hosseinimotlagh et al. (RTSS 2021) - Data-driven structured thermal modeling for COTS multi-core processors
  - This work achieves 61.6% MSE reduction vs [23] (MSE 2.5000 → 0.9640)
- **Liu et al. [8]**: CARTAD - Compiler-assisted RL for thermal-aware task scheduling and DVFS (TCAD 2021)
  - Used XGBoost for latency-related features, lacked integrated multi-method focus for allocation
- **Sasaki et al. [19]**: Intra-task DVFS based on statistical analysis of hardware events (CF 2007)
  - Used decision trees and correlation for energy per instruction, lacked systematic allocation framework
- **Probabilistic methods [15, 16, 17, 18]**: EVT for energy/execution time bounds in DVFS contexts
  - Rarely quantify statistical significance of system metrics for energy or latency bounds
  - This work introduces hybrid statistical learning framework prioritizing feature importance

**Linux Governors**:
- Frequency governor: schedutil (typical configuration)
- DVFS policies target latency, temperature, or energy [8, 9, 10]
- This work prioritizes task-to-core allocation over DVFS for embedded contexts

**Design Trade-offs**:
- **Feature selection overhead**: RF 100 trees, OLS backward stepwise (p-value 0.05) - acceptable for offline profiling phase
- **Accuracy vs parameters**: FCN+RF+BS achieves 61.6% MSE improvement with 16% parameter reduction vs baseline FCN
- **Real-time constraints**: Compressed models (1694-2699 params) enable real-time inference on embedded systems
- **Thermal sensor granularity**: Per-core (Intel) vs cluster-level (Jetson TX2) - methodology adapts to both
- **Random vs correlation-aware**: Correlation excels in subset scenarios (e.g., 10 of 14 cores), random leverages unbiased distribution for full utilization

**Limitations**:
- **Offline profiling phase**: Initial data collection and model training require extensive benchmarking (BOTS suite, 10 runs per configuration)
- **Platform-specific models**: Trained models specific to each processor architecture (Intel Xeon, Core i7 12th Gen, Core i7 8th Gen, Jetson TX2)
- **Benchmark diversity**: BOTS suite (OpenMP tasks) may not fully represent all embedded workloads
- **Thermal throttling**: Performance degradation when cores exceed thermal limits (100°C for Intel Core i7)
- **Cooldown overhead**: 70°C threshold between runs adds latency to experimental workflow

**Future Directions** (implied):
- **Online adaptation**: Real-time model updates based on runtime workload changes
- **Transfer learning**: Adapt models trained on one platform to another with minimal retraining
- **Hardware-software co-design**: Integrate feature selection framework into embedded OS schedulers
- **Extended benchmarks**: Validate on broader workload spectrum (ML inference, video encoding, cryptography)
- **Neuromorphic architectures**: Apply hybrid framework to emerging low-power neuromorphic processors

**Connection to Research Arc**:
- **Follows**: pivezhandi2024flowrl (2024, Flow-augmented few-shot RL), pivezhandi2024work (2024, HMARL for OpenMP DAG)
- **Parallel to**: pivezhandi2025graphperf (2025, Graph-driven performance modeling) - both focus on OpenMP task scheduling
- **Precedes**: pivezhandi2026zerodvfs (2026, Zero-shot LLM-guided allocation), pivezhandi2026hidvfs (2026, Hierarchical multi-agent DVFS)
- **Demonstrates**: Evolution from pure RL approaches (FlowRL, HMARL) to hybrid statistical learning + RL integration
- **Establishes**: Statistical learning foundation for feature-aware allocation, complementing RL-based DVFS in later papers

**Related Work Categories**:

1. **Probabilistic Methods for DVFS** [15, 16, 17, 18]:
   - pWCET (probabilistic worst-case execution time)
   - pWCEC (probabilistic worst-case energy consumption)
   - EVT (extreme value theorem) for upper bounds
   - This work: Hybrid statistical learning framework prioritizing feature importance

2. **Statistical Learning** [19, 15, 8]:
   - Sasaki et al. [19]: Decision trees for DVFS table lookups
   - Cazorla et al. [15]: Hardware counters for energy reduction
   - Liu et al. [8]: XGBoost correlating compiler features with latency
   - This work: Integrates runtime metrics with novel feature selection pipeline

3. **Low-Energy DVFS and Allocation** [25-34]:
   - Xie et al. [25]: Survey of heuristic and ML methods for energy-constrained scheduling
   - Kim & Wu [31]: AutoScale RL for stochastic edge inference
   - Dinakarrao et al. [32]: Thermal-reliability-aware RL for multi-core power management
   - This work: Lightweight statistical model reducing data needs while maintaining accuracy

4. **Few-Shot RL** [35-43]:
   - MAML [40]: Adapts to new tasks with few samples
   - Model-based RL [41]: Approximates dynamics to limit real-world data use
   - Recent: Flow-augmented data generation [13], zero-shot LLM-guided allocation [12]
   - This work: Combines resampling with robust feature selection strategy

5. **Feature Evaluation** [44-48, 4, 1, 46, 47]:
   - Thermal-aware scheduling using utilization or temperature data [44, 45, 4, 1, 46, 47, 48]
   - Studies model transients and ambient conditions [23]
   - This work: Systematic correlation-based feature evaluation, rigorously tested on real hardware

**Code Availability**: Not mentioned in paper

**Hardware Specifications**:

**Intel Core i7 12th Gen (Primary Platform)**:
- 14 cores: 8 P-cores (performance), 6 E-cores (efficiency)
- Hybrid architecture: Alder Lake microarchitecture
- Per-core temperature sensors: via `/sys/class/thermal` or `/sys/devices/platform/coretemp.0/hwmon/hwmon*/temp*_input`
- Thermal throttling point: 100°C
- Frequency range: Not specified (configurable via cpufreq-set)

**Intel Core i7 8th Gen**:
- 6 cores, homogeneous architecture
- Per-core temperature sensors
- Used for validation

**Intel Xeon 2680 v3**:
- 12 cores, homogeneous architecture
- Per-core temperature sensors
- Server-class processor for comparison

**NVIDIA Jetson TX2**:
- 6 cores, big-LITTLE architecture
- 2 Denver cores (big) + 4 ARM Cortex-A57 cores (little)
- Cluster-level temperature: `/sys/class/thermal/thermal_zone*/temp` (aggregated for Denver and A57 clusters)
- On-board power sensors for energy monitoring

**Benchmarks**:
- **Barcelona OpenMP Tasks (BOTS) suite** [22]:
  - fib: Fibonacci (recursive, compute-intensive)
  - strassen: Matrix multiplication (memory-intensive)
  - alignment: Sequence alignment (mixed compute and memory)
  - sparselu: Sparse LU decomposition (mentioned in text)
  - nqueens: N-Queens problem (mentioned in text)

**Energy Monitoring**:
- Intel: `/sys/class/powercap/intel-rapl/` (RAPL - Running Average Power Limit)
- Jetson TX2: On-board power sensors, `/sys/class/thermal/thermal_zone*/energy`

**Performance Metrics Collection**:
- `perf` utility: Hardware performance counters (cache misses, branch misses, CPU cycles, instructions)
- `cpufreq-info`: CPU frequency information
- Custom Python scripts: Task history, temperature buffer, profiler metrics

**Statistical Software**:
- scikit-learn: RandomForestRegressor (100 trees), OLS regression (statsmodels or scikit-learn)
- pandas: Data management and preprocessing
- NumPy: Numerical computations (correlation matrices, bootstrapping)

**Acknowledgements**:
The work was supported by:
- US National Science Foundation: CNS-2301757, CAREER-2306486, CNS-2306745
- US Office of Naval Research: N00014-23-1-2151

**Performance Highlights**:
- **10% energy reduction**: Correlation-aware allocation vs random core selection
- **5°C temperature decrease**: At intermediate utilization (5-9 cores on 14-core system)
- **6% thermal prediction improvement**: Bootstrapping contribution (MSE 0.9808 → 0.9640)
- **16% parameter reduction**: FCN+RF+BS (1694, 2699 params) vs FCN (2014, 3787 params)
- **61.6% MSE improvement**: Over SOTA [23] (MSE 2.5000 → 0.9640)
- **46% profiler feature reduction**: 72 → 39 features with minimal accuracy loss
- **30% temperature feature reduction**: 31 → 21 features with optimal CV error

**Co-Author Expansion**:
- First paper with **Prashant Modekurthy** (UNLV) - indicates expanding research collaborations beyond Iowa State/Wayne State
- Continues collaboration with **Abusayeed Saifullah** (UT Dallas) - PhD advisor since 2023

## Bibdata

```bibtex
% Row 11
@inproceedings{pivezhandi2025feature,
  title={Feature-Aware Task-to-Core Allocation in Embedded Multi-core Platforms via Statistical Learning},
  author={Pivezhandi, Mohammad and Modekurthy, Prashant and Saifullah, Abusayeed},
  year={2025},
  organization={IEEE RTCSA'25 (The 31st IEEE International Conference on Embedded and Real~…}
}
```
