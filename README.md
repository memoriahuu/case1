# Pump Digital Twin

This project models pump performance from flow rate and rotational speed. It combines polynomial ideal pump curves with a neural network that learns the remaining model error (residual).

## Contents

- `Visualize_Pump_Data.ipynb` - explore the measured pump data.
- `Curve_Fitting_Residuals.ipynb` - fit ideal curves and inspect residuals.
- `Pump_Digital_Twin.ipynb` - train and evaluate the residual model.
- `pump_data/` - CSV measurements for 15 pumps and the fitted curve definitions.
- `saved_models/` - trained PyTorch model, scalers, and metadata.

## Model

The saved residual model is a `ResidualNet` with inputs `flow` and `rpm`, followed by two 16-unit ReLU layers and a one-value residual output. Pumps 1-11 are used for training, pumps 12-13 for validation, and pumps 14-15 for testing.

## Getting Started

1. Install the notebook dependencies used by the notebooks, including NumPy, pandas, Matplotlib, scikit-learn, PyTorch, and Jupyter.
2. Open the notebooks in order, or start with `Pump_Digital_Twin.ipynb` to use the saved model.
3. Keep the `pump_data/` and `saved_models/` directories beside the notebooks so their relative paths resolve correctly.

The fitted reference speed is `nref = 0.5`. See `saved_models/model_metadata.json` for the complete model configuration and data split.

## Final Outcome

On the held-out test pumps (14 and 15), the residual-corrected models substantially improve on the ideal polynomial baseline:

| Model | MAE | RMSE | Median absolute error | Within 5% | R2 |
| --- | ---: | ---: | ---: | ---: | ---: |
| Ideal polynomial | 0.030442 | 0.050979 | 2.435% | 63.35% | 0.963204 |
| Polynomial residual | 0.003559 | 0.004883 | 0.805% | 97.92% | 0.999662 |
| Neural-network residual | **0.003034** | **0.004206** | **0.715%** | 95.83% | **0.999750** |

The neural-network residual model achieves the lowest absolute error and highest R2. Its mean absolute percentage error is 1.126%; the worst test point has an absolute percentage error of 11.671%.