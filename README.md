# Robust ML Pipeline with Logging and Error Handling

A machine learning pipeline built the way production systems should be: every
stage is **logged** and **guarded by error handling**, so the pipeline fails
*gracefully and visibly* instead of crashing silently.

The model itself (Iris classification with a decision tree) is deliberately
simple — the point of this project is the **engineering around the model**, not
the model.

## The idea

Most ML tutorials stop at "the model works." This one focuses on what happens
when it *doesn't*: bad data, missing values, training failures. Each stage:

- logs success at **INFO** level,
- catches failures and logs them at **ERROR** level,
- aborts cleanly with a clear message instead of throwing an uncaught exception.

Logs are written to **both the console and `ml_pipeline.log`**, so runs are
auditable after the fact.

## Pipeline stages

```
load → validate → preprocess → split → train → evaluate → predict
```

Each stage is its own function with its own try/except block:

| Stage | Function | Failure handling |
|-------|----------|------------------|
| Load | `load_data()` | Logs and re-raises on load error |
| Validate | `validate_data()` | Checks type, emptiness, missing values |
| Preprocess & split | `preprocess_and_split()` | Fills missing values, 80/20 split |
| Train | `train_model()` | Returns `None` on failure (pipeline aborts) |
| Evaluate & predict | `evaluate_and_predict()` | Logs accuracy + a prediction sample |

## Installation

```bash
pip install pandas scikit-learn
```

## Usage

```bash
python "Robust ML pipeline with logging and error handling.py"
```

This runs the full pipeline end to end, then runs a **failure demonstration**:
it deliberately corrupts a row with a missing value to show validation catching
and *logging* the problem instead of crashing.

## Example log output

```
2026-05-28 10:00:01 - INFO - Loading dataset...
2026-05-28 10:00:01 - INFO - Dataset loaded successfully (150 rows).
2026-05-28 10:00:01 - INFO - Data validation successful.
2026-05-28 10:00:01 - INFO - Test accuracy: 1.00
2026-05-28 10:00:01 - INFO - Pipeline completed successfully.
2026-05-28 10:00:01 - ERROR - Data validation error: Missing values detected in the dataset.
```

## Tech stack
Python · scikit-learn · pandas · Python `logging`
