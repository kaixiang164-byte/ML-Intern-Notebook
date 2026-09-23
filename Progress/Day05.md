# Day 05 — Model Generalization

## LeetCode 121 — Best Time to Buy and Sell Stock
- Implemented a brute-force solution that checks every valid buy/sell pair.
- Brute-force time complexity: O(n²).
- Optimized to a single pass by tracking the lowest previous price and the maximum profit.
- Optimized complexity: O(n) time and O(1) extra space.

## Machine Learning
- Distinguished the roles of training, validation, and test sets.
- Learned about underfitting, overfitting, bias, and variance.
- Understood the squared-error decomposition:
  Expected prediction error = Bias² + Variance + Noise variance.
- Performed four-fold cross-validation.
- Learned Ridge regularization and selected among candidate alpha values.
- Understood that standardization parameters must be estimated using only the training data within each fold.

## Experimental Setup
- Development set: 64 samples.
- Held-out test set: 16 samples.
- Four-fold CV: 48 training samples and 16 validation samples per fold.
- Used the same folds to compare models.

## Results

| Model | Mean CV MSE |
|---|---:|
| Degree 2, ordinary regression | 8.047 |
| Degree 15, ordinary regression | 1424.736 |
| Degree 15, standardized Ridge, alpha=0.1 | 9.740 |
| Degree 15, standardized Ridge, alpha=1 | 8.282 |
| Degree 15, standardized Ridge, alpha=10 | 11.145 |

## Final Model
- Selected degree-2 ordinary regression because it had a slightly lower mean CV MSE and was simpler.
- The small CV difference from Ridge with alpha=1 was not established as statistically significant.
- Refit the selected model on all 64 development samples.
- Final test MSE: 7.414.
- Did not use the test result to make further modeling decisions.

## Key Takeaways
- Lower training error does not guarantee better generalization.
- A single validation split can give an incomplete picture of model performance.
- Regularization can improve stability, but its strength still requires validation.
- Stronger regularization is not always better.
- Keep the test set separate until model selection is complete.
- A finite test-set MSE can fall below the noise variance because of sampling variability.