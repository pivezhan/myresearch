# Statistical based neural network in human activity recognition

**Citation Key**: `pivezhandi2015statistical`

**Year**: 2015

**Type**: article

**Authors**: Pivezhandi, Mohammad and Maybodi, Baback Mazloom-Nezhad

**Venue**: International Journal of Computer Applications

---

## Key Points

- Statistical neural network approach for Human Activity Recognition (HAR) achieving >99% mean accuracy
- Dimensionality reduction using P-value evaluation in feature space (reduced to 52 features)
- Hybrid model combining statistical feature compression with Neural Network architecture
- Comparison of Decision Trees (Bagging, Random Forest, Boosting) vs Neural Networks
- Best performance: Random Forest and Boosting with 99.43% and 99.41% test accuracy respectively
- Proposed architecture suitable for hardware implementation in gesture recognition applications

## Abstract

This research proposed an efficient method in classification of Human Activity Recognition tasks. The evaluated tuned models show higher than 99 percent mean accuracy and gain more training and testing accuracy in comparison to previous studies.

Dimensionality reduction was introduced based on P-value evaluation in feature space. The approach compares multiple machine learning models including Decision Trees (Bagging, Random Forest, Boosting), Neural Networks, and a novel hybrid approach.

The hybrid model combines statistical compression in feature space alongside Neural Network architecture. By applying statistical procedures like P-value significant feature extraction, important features were selected and applied to the multi-layered neural network model.

The final model could be used as best architecture of hardware implementation in gesture recognition applications, achieving 2% higher accuracy than previous studies on the same dataset.

## Contributions

1. **P-value based dimensionality reduction**: Statistical feature selection method reducing feature space from original to 52 significant features based on P-value evaluation
2. **Comprehensive model comparison**: Systematic evaluation of Decision Trees (Bagging, Random Forest, Boosting), Neural Networks, and hybrid approaches for HAR classification
3. **Hybrid statistical-neural model**: Novel combination of Random Forest feature importance ranking (top 32 features) with Neural Network architecture, achieving 94.76% test accuracy
4. **Improved accuracy**: 2% higher classification accuracy compared to previous work on the same dataset (Velloso et al. 2013)
5. **Hardware-oriented architecture**: Proposed model designed for efficient hardware implementation in gesture recognition applications

## Methodology

**Dataset**:
- Human Activity Recognition (HAR) weight lifting exercises
- 6 participants (ages 20-28)
- 4 wearable sensors (arm, forearm, belt, dumbbell)
- 5 activity classes (A-E): Class A = correct execution, Classes B-E = common mistakes
- 52 features after data cleaning (removal of unnecessary and unassigned data)
- Tools: R for statistical analysis, MATLAB for Neural Networks

**Model 1: Decision Trees**:
- **Bagging**: Bootstrap aggregating with majority vote classification
- **Random Forest**:
  - Tuned with m ≈ √p features per split
  - Optimum: ~7 variables per split (Figure 4)
  - Feature importance via mean decrease in accuracy
  - Out-of-Bag (OOB) error estimation
- **Boosting**: Generalized Boosted Model (GBM)
  - Tuning parameters: number of trees, shrinkage value (λ), interaction depth (d)
  - Sequential tree addition (not independent like bagging)
  - Regularization to prevent overfitting

**Model 2: Multi-Layer Perceptron Neural Network**:
- **Architecture**: 3 layers (input, hidden, output)
- **Hidden layer**: 26 neurons
- **Cost function**: Regularized logistic regression (Equation 3 in paper)
- **Regularization parameter**: Tuned to prevent overfitting (Figure 7)
- **Data split**: 60% training, 20% cross-validation, 20% testing
- **Optimization**: Backpropagation with weight shrinkage

**Model 3: Hybrid Statistical-Neural Model**:
- Step 1: Random Forest feature importance ranking (mean decrease in accuracy)
- Step 2: Select top 32 features (from Table 1: magnet_dumbbell_y, roll_belt, magnet_dumbbell_z, etc.)
- Step 3: Train Neural Network on selected features only
- Advantage: Preserves feature interpretability (unlike PCA)

**Model 4: Neural Network with PCA**:
- Principal Component Analysis dimensionality reduction
- Top 32 eigenvalues retained
- Features mapped to new dimensional space

## Results

**Model Performance (Table 2)**:

| Model | Train Accuracy | Test Accuracy |
|-------|----------------|---------------|
| Bagging | 78.37% | 77.26% |
| Random Forest | 100% | 99.43% |
| Boosting | 100% | 99.41% |
| Neural Network (52 features) | 98.59% | 96.85% |
| NN with PCA (32 components) | 96.54% | 93.98% |
| Hybrid (top 32 features) | 95.81% | 94.76% |

**Key Findings**:
- **Best models**: Random Forest and Boosting achieve ~99.5% test accuracy with 100% training accuracy
- **Fastest convergence**: Random Forest shows fastest testing error convergence with small number of trees (Figure 6)
- **Feature importance**: Top 3 predictors based on mean decrease in accuracy (Table 1):
  1. magnet_dumbbell_y (accuracy decrease: 0.1232, Gini: 546.32)
  2. roll_belt (accuracy decrease: 0.1210, Gini: 924.30)
  3. magnet_dumbbell_z (accuracy decrease: 0.1160, Gini: 625.11)
- **Hybrid vs PCA**: Hybrid model (94.76% test) outperforms PCA (93.98% test) with same number of features (32)
- **Dimensionality reduction**: Feature selection stabilizes at ~20 features (Figure 8), though 32 features used for comparison

**Comparison with Literature**:
- Achieved 2% higher accuracy than equivalent model in Velloso et al. 2013 (baseline dataset source)
- Tuned decision trees significantly outperform traditional methods

## Notes

**Research Context**:
- **First ML publication** by Mohammad Pivezhandi (2015)
- **Affiliation**: Department of Electrical and Computer Engineering, Shahid Beheshti University, Tehran, Iran
- **Co-author**: Baback Mazloom-Nezhad Maybodi

**Application Domain**:
- Human Activity Recognition (HAR)
- Gesture recognition for hardware implementation
- Weight lifting exercise quality assessment
- Potential applications: athlete technique monitoring, elderly care, Parkinson's disease detection

**Dataset Reference**:
- Based on Velloso et al. 2013 dataset: "Qualitative activity recognition of weight lifting exercises"
- Available at: http://groupware.les.inf.puc-rio.br/har

**Technical Implementation**:
- **R packages**: adabag (bagging/boosting), randomForest, gbm (generalized boosted models), RFpermute (feature importance)
- **MATLAB**: Neural Network Toolbox for MLP implementation
- **Statistical methods**: P-value hypothesis testing, Gini index, cross-entropy

**Key Innovation**:
- Hybrid model provides better compression than PCA while maintaining feature interpretability
- Mean decrease in accuracy from Random Forest used as input variable selection for Neural Network
- Important features not transformed (unlike PCA), allowing direct interpretation

**Limitations**:
- Dataset limited to 6 participants
- Controlled laboratory environment (not real-world deployment)
- Sensor-based approach requires wearable devices

**Future Directions** (implied):
- Hardware implementation of proposed hybrid architecture
- Real-time gesture recognition systems
- Extension to other HAR applications beyond weight lifting

**Connection to Later Work**:
- Establishes Mohammad's foundation in machine learning and pattern recognition
- Precursor to later DVFS and energy-aware scheduling research (2023-2026)
- Demonstrates early interest in hardware-efficient ML implementations

## Bibdata

```bibtex
@article{pivezhandi2015statistical,
  title={Statistical based neural network in human activity recognition},
  author={Pivezhandi, Mohammad and Maybodi, Baback Mazloom-Nezhad},
  journal={International Journal of Computer Applications},
  volume={124},
  number={12},
  year={2015},
  publisher={Foundation of Computer Science}
}
```
