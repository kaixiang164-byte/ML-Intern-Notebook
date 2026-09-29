# Day 6 — Two Pointers and Decision Tree Regression

## Completed

- [x] Solve LeetCode 283 — Move Zeroes in place.
- [x] Explain time complexity and auxiliary space complexity.
- [x] Understand regression-tree splits and leaf predictions.
- [x] Distinguish SSE from MSE.
- [x] Compare tree depths using cross-validation.
- [x] Refit the selected model and evaluate it on held-out test data.
- [x] Practice explaining model selection in English.

## LeetCode 283 — Move Zeroes

Move all zeros to the end while preserving the relative order of nonzero values.

- Scan the list once.
- Maintain `next_pos`, the index where the next nonzero value should go.
- When a nonzero value is found, swap it with the value at `next_pos`, then increment `next_pos`.
- `next_pos` also equals the number of nonzero values already placed at the front.

Examples:

```text
[0, 1, 0, 3, 12] -> [1, 3, 12, 0, 0]
[0, 0, 1]       -> [1, 0, 0]
```

**Time complexity:** O(n), because the algorithm scans the list once.

**Auxiliary space complexity:** O(1), because it modifies the original list without building another list. Auxiliary space excludes the input itself.

## Decision Tree Regression

Under squared-error loss, a regression tree chooses splits to reduce the combined squared error of the child nodes. Each leaf predicts the mean target value of its training observations.

Toy training data:

| Study hours | Score |
|---:|---:|
| 1 | 50 |
| 2 | 55 |
| 3 | 80 |
| 4 | 85 |

For a split at 2.5:

- Hours <= 2.5: predict 52.5.
- Hours > 2.5: predict 82.5.
- Training SSE = 25; training MSE = 25 / 4 = 6.25.

A split at 1.5 gives SSE approximately 516.67, so the split at 2.5 is better for these observations.

```text
SSE = sum((actual - predicted)^2)
MSE = SSE / number_of_observations
```

With `max_depth=2`, the tree predicts `[50, 55, 80, 85]` on this toy training set. Both training SSE and training MSE are zero. A perfect training fit does not guarantee accurate predictions on new data; it can include fitting noise.

`max_depth` limits the number of decisions along a root-to-leaf path. `max_depth=None` removes this depth limit, while other stopping conditions still apply.

## Model Selection Experiment

Generate a fresh sample of 80 observations using NumPy random seed 6:

$$
X \sim \operatorname{Uniform}(-3, 3), \qquad
Y = 3X^2 + \varepsilon, \qquad
\varepsilon \sim N(0, 9).
$$

The noise has standard deviation 3 and variance 9.

- Development set: 64 observations.
- Test set: 16 observations, reserved until model selection was complete.
- Train/test split: `test_size=0.2`, `random_state=42`.
- Four-fold cross-validation on the development set: 48 training and 16 validation observations per round.
- A fresh tree is fitted in each fold. `cross_val_score` does this automatically.
- `scoring="neg_mean_squared_error"` returns negative MSE; negate the scores to obtain ordinary MSE.

### Recorded Cross-Validation Results

The candidate trees used `random_state=42` and the same four folds.

| Maximum depth | Fold 1 MSE | Fold 2 MSE | Fold 3 MSE | Fold 4 MSE | Mean CV MSE |
|---|---:|---:|---:|---:|---:|
| 1 | 63.0801 | 110.3989 | 62.9057 | 34.5643 | 67.7372 |
| 3 | 14.5784 | 33.2522 | 28.2062 | 13.1344 | 22.2928 |
| None | 14.7008 | 33.8685 | 32.4772 | 17.7915 | 24.7095 |

Depth 3 had the lowest mean CV MSE among the three candidates tested. The depth-1 tree was too simple for this curved relationship. Removing the depth limit did not improve validation performance in this experiment, which is consistent with overfitting. This does not establish that unrestricted trees always perform worse.

### Final Test Evaluation

Refit the selected depth-3 tree on all 64 development observations, then evaluate it on the 16 test observations.

**Reported test MSE: 14.859388178954454.**

The reported final run used `DecisionTreeRegressor(max_depth=3)` without an explicit `random_state`. For reproducibility, future runs should explicitly set `random_state=42`, as in the CV comparison.

The CV and test errors need not match: they use different evaluation observations, and each CV model trained on 48 observations while the final model trained on 64. The test result was not used to choose the depth.

## Interview Practice

> I chose a maximum depth of 3 because it had the lowest mean cross-validation MSE among the models I tested. Removing the depth limit increased the CV MSE from 22.29 to 24.71, suggesting that the additional complexity did not improve generalization.

Useful terms: **in-place modification**, **auxiliary space**, **split threshold**, **leaf prediction**, **underfitting**, **overfitting**, **generalization**, and **computational cost**.

A deeper tree may require more computation and memory, but computational cost was not measured or used to select the model in this experiment.

## Files Used During the Lesson

- `Leetcode/day06_two_pointers.ipynb`
- `Python/day06_decision_trees.ipynb`

Results above summarize the code and outputs reported during the lesson.
