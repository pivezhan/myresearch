# Accuracy improvement with reliable statistical-based models for CNT-FET applications

**Citation Key**: `pivezhandi2017accuracy`

**Year**: 2017

**Type**: article

**Authors**: Pivezhandi, Mohammad and Abedi, Kambiz and Hassanzadeh, Alireza

**Venue**: Journal of Computational Electronics

---

## Key Points

- Statistical-based models (SVR, Decision Trees) for CNT-FET device modeling achieving 8× better accuracy than compact models
- Random Forest achieves ~9% error for off-state current (IOFF), dramatically better than previous 84.3% error in compact models
- 10× faster learning time: Random Forest trains in 53 seconds vs 585 seconds for best ANN (nnet package)
- Decision Trees provide convex solutions avoiding local optima problem inherent in ANNs
- Comprehensive comparison of ANN, Support Vector Regression (SVR), Random Forest (RF), and Boosting on 5376 CNT-FET samples
- Tenfold cross-validation for all model selection and tuning procedures
- R programming environment with multiple packages (neuralnet, nnet, randomForest, e1071, dismo, gbm)

## Abstract

In this work, different statistical-based algorithms for modeling of carbon nanotube (CNT) field-effect transistors (FETs) are analyzed and implemented. CNT-FETs have complicated structures; thus simulating their models requires significant computational time.

In previous studies, many researchers have concentrated on artificial neural networks (ANNs) to obtain accurate models that expedite simulation. However, due to the local optima problem, these designs require a trial-and-error approach and are less accurate than more recently developed algorithms.

Therefore, we have utilized support vector regression (SVR) to obviate the local optima problem, and have used decision trees (DTs) to ensure accuracy. The salient features of qualitative-based DT techniques include their accuracy and speed, as they propose a convex solution and do not require preliminary conditioning.

Tenfold cross-validation is considered to yield infallible parameters for all of the analyzed techniques. Thus, the corresponding models are almost identical when compared with each other. Among the analyzed techniques, the random forest (RF) algorithm exhibits around 9% error for off-state current, i.e., eightfold greater accuracy than the previous compact model.

The RF learning time is also more than ten times shorter than the fastest ANN package. All the plots and simulation results in this study are extracted from the R programming environment.

## Contributions

1. **Comprehensive statistical modeling comparison**: Systematic evaluation of five learning techniques (ANN with neuralnet and nnet packages, SVR, Random Forest, Boosting) for CNT-FET transistor modeling with 5376 samples
2. **Superior accuracy in subthreshold region**: Random Forest achieves 9.72% error for off-state current (IOFF) compared to 84.3% error in previous compact model [Dokania 2016], representing 8× accuracy improvement
3. **Dramatic speedup over ANNs**: RF trains in 53 seconds (10× faster than nnet's 585 seconds, 26× faster than neuralnet's 1394 seconds) while maintaining comparable test error (4.45% vs 3.47% for nnet)
4. **Convex solution methodology**: SVR and Decision Trees eliminate local optima problem inherent in ANNs, providing reproducible results without trial-and-error parameter tuning
5. **Bootstrapping and resampling for small datasets**: Decision Trees use bootstrap aggregating (bagging) to enlarge limited training data, improving generalizability with Out-of-Bag (OOB) error estimation
6. **SPICE-compatible implementation framework**: Proposed models can be extended to circuit simulators (Figure 1 shows implementation schematics for ANN, SVR, and DT in hardware)
7. **Comprehensive tuning methodology**: Tenfold cross-validation protocol for all models ensuring fair comparison and optimal hyperparameter selection

## Methodology

**Dataset and Features**:
- **Total samples**: 5376 raw datasets extracted from CNT-FET simulators [Guo 2004, Koswatta 2006, Budiman 2014]
- **Input features** (5 predictors for drain current Ids):
  1. n: Carbon nanotube chirality vector (zigzag CNT: (n,0))
  2. tins: Oxide thickness (nm)
  3. LG: Channel gate length (nm)
  4. Vds: Drain-to-source voltage (V)
  5. Vgs: Gate-to-source voltage (V)
- **Target variable**: Ids (source/drain current in Amperes)
- **Additional dataset**: 2000 samples with simplified features (n, Vds, Vgs only) for focused analysis

**R Programming Environment**:
- **Version**: R 3.2.0
- **Packages used**:
  - ANN: neuralnet (resilient backpropagation), nnet (simple efficient implementation)
  - SVR: e1071, kernlab
  - Decision Trees: randomForest, dismo (boosting), gbm (generalized boosted models)
  - Utilities: caret (model selection), cvTools (cross-validation), ggplot2/grid/gridExtra (visualization), dplyr/plyr (data manipulation)

**Model 1: Artificial Neural Networks (ANNs)**:

**Architecture and Equations**:
- Risk function (cost function):
  J(θ) = (1/2m) Σ[i=1 to m] cost(hθ(x^(i)), y^(i)) + (λ/2m) Σ[j=1 to n] θj²
  - hθ(x^(i)): ANN output for sample i
  - y^(i): Target value for sample i
  - λ: Regularization parameter (prevents overfitting)
  - θj: Weight values

- Activation function: Tangent hyperbolic (output range: -1 to 1)
- Input normalization: Min-max mapping
  X' = [(X - min)/(rmmax - min)] × (max_hat - min_hat) + min_hat
  - New range: [-1, 1]

**Tuning procedure** (Figure 2):
- Tenfold cross-validation for number of hidden layer neurons
- Tested: 30 to 100 neurons in single hidden layer
- Optimal: 75 neurons (balance between under-fitting and over-fitting)
- Under-fitting: <30 neurons (high CV and training errors)
- Over-fitting: >90 neurons (low training error, high CV error)
- Runtime scales with neurons: 75 neurons much faster than 95 neurons

**Two packages compared**:
- **neuralnet**: Three versions of resilient backpropagation + traditional backpropagation, flexible with multiple layers
- **nnet**: Simple, efficient, single hidden layer, regularization parameter tuning

**Model 2: Support Vector Regression (SVR)**:

**Convex Optimization Approach**:
- Maps input to higher dimensions where complex boundaries become linear
- Avoids local optima problem of ANNs through convex dual optimization

**Lagrange Multiplier Formulation**:
Maximize: -(1/2) Σ[i,j] (αi - αi*) ⟨xi, xj⟩ - ε Σ[i] (αi + αi*) + Σ[i] yi(αi - αi*)

Constraints: Σ[i] (αi - αi*) = 0 and αi, αi* ∈ [0,C]

**Support Vector Expansion Function**:
S(x) = Σ[i=1 to l] (αi - αi*) ⟨x̂i, x⟩ + b
- x̂i: Support vectors
- αi, αi*: Lagrange multipliers (determine importance of each data point)
- b: Bias value
- C: Regularization parameter (proportional to 1/λ)

**Kernel Function** (maps to higher dimensions):
- Radial Basis Function (RBF): K(x, x̂) = exp(-||x - x̂||²/2σ²)
- σ: Standard deviation (controls sensitivity to input variables)
- Higher σ → smoother function → more under-fitting risk

**Simplified risk function**:
S(θ) = C Σ[i=1 to m] cost(hθ(K^(i)(x̂,x)), y^(i)) + (1/2) Σ[j=1 to n] θj²

**Tuning procedure** (Figure 3):
- Tenfold CV for σ (sigma) and C parameters
- Tested: σ ∈ {0.06, 0.09, 0.1, 0.3, 0.6, 0.9}, C ∈ {100, 300, 600, 900, 1000, 3000, 6000, 9000}
- Optimal: σ = 0.3, C = 3000 (minimum CV error)
- e1071 package used for implementation

**Model 3: Decision Trees (Random Forest, Boosting)**:

**Key Advantages**:
- Qualitative features (no normalization required)
- Visual interpretability (tree structure)
- Convex solution (no local optima)
- Bootstrap resampling (augments small datasets)

**Bootstrap Aggregating (Bagging)**:
- Sampling with replacement from ~2/3 of original data
- Remaining 1/3 used for Out-of-Bag (OOB) error: MSEOOB = Σ[i=1 to N] (yi - ŷiOOB)²
- Final prediction: Average of B trees
  f̂avg = (1/B) Σ[b=1 to B] f̂^b(x)

**Residual Sum of Squares minimization**:
RSS = Σ[j=1 to J] Σ[i∈Rj] (yi - ŷRj)²
- Rj: Predictor regions (terminal leaves)
- ŷRj: Mean response in region j

**Random Forest (RF)**:
- Considers p = √n random variables at each split (n = total predictors)
- Typical: p = √5 ≈ 2-3 features per split
- Advantages: Fast convergence (Figure 5a), reduced variance, higher accuracy
- No tuning parameters required (OOB error used)

**Boosting** (Algorithm 1):
- Sequential tree growing with learning rate λ (shrinkage)
- Tuning parameters:
  - λ: Shrinkage value (learning rate)
  - d: Depth (number of splits, d+1 terminal nodes)
  - B: Number of trees
- Slower than RF but provides general model avoiding over-fitting
- Algorithm iteratively updates residuals and adds shrunken trees

**Tuning procedure** (Figure 4):
- Tenfold CV for depth (d ∈ {4,5,6,7,8}) and shrinkage (λ ∈ {0.03, 0.06, 0.09, 0.1, 0.3, 0.6, 0.9})
- Optimal parameters determined from minimum CV error
- dismo package (generalized boosted models) used

**Model Selection Technique**:
- **Tenfold Cross-Validation**: Data split into 10 random subsets, 9/10 for training, 1/10 for testing, averaged over 10 iterations
- Kohavi [1995]: Tenfold CV is optimal for model selection with negligible computational difference vs higher fold counts

## Results

**Performance Comparison** (Table 1 - Mean Percent Error on 5376 samples):

| Model | Test MPE (%) | Train MPE (%) | Learning Time (sec) |
|-------|-------------|---------------|---------------------|
| Random Forest | 4.45 | 2.29 | **53** (fastest) |
| Boosting | 3.58 | 2.43 | 84 |
| ANN (nnet) | **3.47** (best) | 3.13 | 585 |
| SVR | 6.03 | 5.30 | 88 |
| ANN (neuralnet) | 5.64 | 5.68 | 1,394 (slowest) |

**Mean Percent Error formula**:
MPE% = 100 × (1/n) Σ[i=1 to n] |Yi - Ŷi| / |Yi|

**Key Findings**:
- **Best test accuracy**: nnet (3.47%) with 75 neurons in hidden layer
- **Best speed**: Random Forest (53 sec, 11× faster than nnet, 26× faster than neuralnet)
- **Best speed-accuracy tradeoff**: RF achieves 4.45% error in 53 sec
- **Data-dependent consistency**: DT and SVR produce reproducible results (no random variation like ANNs)

**IV Characteristics Analysis** (Figure 6):
- Linear IV curves show all models fit experimental data well in on-state (above-threshold) region
- CNT configuration: (17,0) chirality, LG=20nm, Tox=3nm
- Vgs sweep: 0.25V to 0.5V in steps of 0.0625V
- Vds sweep: 0 to 0.5V

**Subthreshold Region Performance** (Table 2 - Detailed metrics):

| Metric | Experimental | Compact Model | ANN (nnet) | SVR | Random Forest |
|--------|-------------|---------------|------------|-----|---------------|
| **SS error** (mV/dec) | - | 11.12% | 4.05% | 92.90% | **25.88%** |
| **ION error** | - | 3.89% | **0.37%** | 4.67% | 4.06% |
| **IOFF error** | - | 84.3% | 66.84% | 251.27% | **9.72%** (best) |
| **Runtime** (sec) | 174 | 3.29 | ≪1 | ≪1 | ≪1 |

Where:
- **ION**: Current when VGS = 1V, VDS = 1V (on-state)
- **IOFF**: Current when VGS = 0V, VDS = 1V (off-state)
- **SS** (Subthreshold Swing): [d log(Id)/dVgs]^(-1) (mV/decade)

**Subthreshold Region Accuracy** (Figure 8):
- **Random Forest dominates**: 9.72% IOFF error vs 84.3% (compact model), 66.84% (nnet), 251.27% (SVR)
- 8× accuracy improvement over previous compact model [Dokania 2016]
- Log-linear IV characteristics: RF shows best conformity to experimental data in subthreshold region
- Configuration: (25,0) chirality, LG=30nm, Tox=9nm

**Feature Importance Analysis** (Figure 5b, 5d):
- **Most influential**: Chirality (n) - highest MSE increase in RF, highest relative influence in Boosting
- **Least influential**: Oxide thickness (Tins) - minimal impact on both algorithms
- **Ranking**: n > Vgs > Vds > LG > Tins

**Linear Transfer Characteristics** (Figure 7):
- 2000 samples with simplified features (n, Vds, Vgs)
- Vds values: 0.11V, 0.33V, 0.78V, 1.0V
- All models show high conformity to experimental data in on-state region
- Gate voltage sweep: -0.5V to 1.5V

**Convergence Analysis** (Figure 5a, 5c):
- **RF**: Rapid error reduction, stable after ~250 trees
- **Boosting**: Smooth error reduction, slower convergence
- RF achieves fast training with minimal overfitting risk

**ANN Sensitivity to Tuning** (Figure 2):
- High dependency on number of hidden neurons
- Optimal around 75 neurons (CV error minimum)
- Runtime increases significantly with neuron count
- Trial-and-error required for each new dataset

**SVR Parameter Sensitivity** (Figure 3):
- Complex interaction between σ (sigma) and C parameters
- Optimal at σ=0.3, C=3000
- Different σ values show varying error patterns across C range
- Requires systematic grid search for tuning

## Notes

**Research Context**:
- **Third publication** by Mohammad Pivezhandi (2017)
- **Affiliation**: Department of Electrical Engineering, Shahid Beheshti University, Tehran, Iran
- **Co-authors**: Kambiz Abedi (corresponding author), Alireza Hassanzadeh
- **Journal**: Journal of Computational Electronics, Springer
- **DOI**: 10.1007/s10825-017-1026-3
- **Published online**: August 16, 2017
- **Pages**: 610-619 (10 pages)

**Application Domain**:
- Carbon Nanotube Field-Effect Transistor (CNT-FET) device modeling
- Alternative to computationally expensive NEGF (Non-Equilibrium Green's Function) quantum transport simulators
- Circuit design applications with SPICE integration potential
- Nanoscale transistor designs for post-CMOS technology

**CNT-FET Background**:
- Silicon ICs approaching scaling limits
- CNTs solve channel length barrier for further CMOS scaling
- Higher mobility → faster devices
- Proposed as CMOS alternative for next-generation devices
- Compact models have high error rates and case-dependent designs
- Learning techniques provide lower computational cost than iterative simulators

**Technical Innovation**:
- **Convex optimization advantage**: SVR and DTs eliminate local optima problem plaguing ANNs
- **No normalization required**: Decision Trees work directly with qualitative features
- **Bootstrap resampling**: Artificially enlarges small datasets (2/3 sampling with replacement)
- **OOB error estimation**: Equivalent to leave-one-out CV without additional computation cost
- **Hardware implementation framework** (Figure 1):
  - ANN: Requires fitted weight values
  - SVR: Requires weights + kernel function with support vectors
  - DT: Requires conditional values (decision boundaries)

**Comparison with Previous Work**:
- **Hatami et al. [2004]**: First ANN for SOI MOSFET modeling
- **Hayati et al. [2010]**: ANN for CNT-FET modeling (predecessor work)
- **Dokania et al. [2016]**: Analytical compact model with 84.3% IOFF error
- **This work**: Achieves 9.72% IOFF error with RF (8× improvement)

**Key Advantages of Statistical Methods over ANNs**:
1. **Reproducibility**: Same dataset produces same model (no random initialization)
2. **Convexity**: Global optimum guaranteed (no local optima traps)
3. **Speed**: 10-26× faster training than ANNs
4. **No preprocessing**: Decision Trees require no normalization
5. **Interpretability**: Tree structure provides visual understanding
6. **Feature importance**: Automatic ranking of predictor variables

**Limitations and Challenges**:
- **ANNs**: Local optima problem, trial-and-error tuning, random outputs, long training time, require normalization
- **SVR**: High MPE compared to other methods (6.03%), still requires tuning for σ and C
- **Compact Models**: Very poor subthreshold region accuracy (84.3% IOFF error), case-dependent design
- **NEGF Simulators**: Extremely high computational cost (174 sec vs <1 sec for trained models)

**Future Work** (from conclusion):
- Make DT algorithms compatible with hardware-based designs (SPICE integration)
- Enhance ability to emulate DC behavior of CNT-FETs
- Extend to other nanoscale transistor modeling methods
- Improve subthreshold region modeling further

**R Packages and Tools**:
- **neuralnet** [Günther & Fritsch 2010]: Resilient backpropagation with multiple variants
- **nnet** [Venables & Ripley 2002]: Simple, efficient single hidden layer
- **e1071** [Meyer et al. 2017]: SVR with RBF kernel
- **randomForest** [Breiman 2001]: Bootstrap aggregating with random feature selection
- **dismo** [Hijmans et al. 2016]: Boosting implementation
- **gbm** [Ridgeway 2007]: Generalized boosted models
- **caret**: Model training and tuning framework

**Statistical Methods**:
- **Tenfold CV** [Kohavi 1995]: Optimal for model selection
- **Bootstrap** [Efron & Tibshirani 1994]: Resampling for small datasets
- **Bagging** [Breiman 1996]: Bootstrap aggregating for variance reduction
- **Random Forest** [Breiman 2001]: De-correlated trees with random feature selection
- **Boosting**: Sequential tree growing with shrinkage

**Connection to Research Arc**:
- Follows pivezhandi2016asip (hardware design for signal processing)
- Establishes machine learning and statistical modeling expertise
- Transition from pure hardware (ASIP) to ML-based device modeling
- Foundation for later work in statistical learning (pivezhandi2025feature - Feature-Aware allocation)
- Precursor to advanced ML techniques in DVFS research (2023-2026)

**Subthreshold Swing Equation**:
SS = [d log(Id)/dVgs]^(-1)
- Units: mV/decade
- Lower is better (steeper slope)
- Critical for low-power applications

**Model Characteristics Summary**:
- **ANN (nnet)**: Best test accuracy (3.47%), slow training (585s), requires tuning, random outputs
- **ANN (neuralnet)**: Poorest performance (5.64% test), very slow (1394s), flexible architecture
- **SVR**: Moderate accuracy (6.03%), fast training (88s), convex solution, poor subthreshold
- **Random Forest**: Excellent speed-accuracy tradeoff (4.45% in 53s), best subthreshold (9.72% IOFF), reproducible
- **Boosting**: Best training error (2.43%), moderate time (84s), requires careful tuning

## Bibdata

```bibtex
@article{pivezhandi2017accuracy,
  title={Accuracy improvement with reliable statistical-based models for CNT-FET applications},
  author={Pivezhandi, Mohammad and Abedi, Kambiz and Hassanzadeh, Alireza},
  journal={Journal of Computational Electronics},
  volume={16},
  number={3},
  pages={610--619},
  year={2017},
  publisher={Springer}
}
```
