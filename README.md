# Remaining Useful Life prediction on NASA C-MAPSS (FD001)

Predicting how many cycles a turbofan engine can still run before failure,
from sensor data. Personal learning project in machine learning.

## Problem
Supervised regression: predict the Remaining Useful Life (RUL) from
2 operating settings and 15 sensors (17 features after cleaning).

## Data
NASA C-MAPSS, subset FD001 (100 training engines, 100 test engines):
https://data.nasa.gov/dataset/cmapss-jet-engine-simulated-data

The data is not included in this repository. Download it and place
`train_FD001.txt`, `test_FD001.txt` and `RUL_FD001.txt` in a `data/` folder.

## Method
- RUL computed as (last cycle - current cycle), capped at 125
- 7 constant columns removed (op3, s1, s5, s10, s16, s18, s19)
- Standardization fitted on the training set only (no data leakage)
- Train/validation split by engine, not by row (80 / 20 engines)
- Test: last cycle of each test engine, compared with the provided RUL

## Results
| Model | RMSE validation | RMSE test |
|---|---|---|
| Mean baseline | 41.72 | - |
| Linear regression | 20.41 | 20.79 |
| Random forest | 17.01 | 17.11 |
| Gradient boosting | 16.90 | 17.24 |
| Neural network (MLP) | 16.73 | 17.22 |

## Key findings
- Nonlinear models reduce the error by about 3.5 cycles compared with
  linear regression (20.8 to about 17.2 cycles on the test set).
- The three nonlinear models perform almost identically, which suggests
  the limit comes from the information available (one time step per
  prediction) rather than from the choice of algorithm.
- Sensor s11 is by far the most important feature (about 62% of the random
  forest importance), followed by s9 and s4.
- Validation and test errors are close, so the models generalize to unseen engines.

## Limitations
- Models see one time step at a time, without the engine's history
- Only the FD001 subset (one operating condition, one fault mode)
- Feature importance of a random forest can be shared arbitrarily
  between correlated sensors

## Possible improvements
- Rolling-window features, or a recurrent / convolutional network
- Other subsets (FD002 to FD004)

## Run it
```
pip install -r requirements.txt
```
Then run the notebooks in order: `01_exploration`, `02_preparation`, `03_baseline`.